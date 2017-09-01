最近开发的运维平台需要接入公司的统一认证平台，实现单点登录。

### 单点登录实现原理

当用户(浏览器)访问我们的服务(第三方应用)时， 服务首先判断用户是否已经登录（其实就是判断请求中是否有`sessionid`），如果没有登录，则重定向至认证服务器，重定向过程中将原始URL携带至认证服务器。

认证服务器接受用户验证信息，如通过，则重定向至原始URL，并携带随机生成的code信息。

服务获取code与原始URL请求后，再使用key和secret从认证服务器获取token。获取token信息之后创建session。并在响应中添加`Set-Cookie`字段。

用户接收响应之后，设置会话cookie，也就是`sessionid`。

当用户再次访问服务时，携带cookie，所以服务会判断用户已经登录，从而实现直接访问。

当退出登录时，需要同时清除服务的cookie和认证服务器的cookie，一般通过调用认证服务器的登出接口实现。

### Django实现过程

为了在Django中接入Oauth，先得去掉Django提供的session服务和认证服务。然后自定义一个中间件。

中间件其实就是在捕获请求，在请求前判断用户是否已经登录，并获取token信息，创建session，在请求后保存session，并根据需要来删除cookie信息。

```
# coding:utf-8
import time
import urllib
from importlib import import_module
from django.conf import settings
from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponseRedirect
from django.utils.cache import patch_vary_headers
from django.utils.http import cookie_date
from django.contrib.sessions.backends.base import UpdateError
from django.core.exceptions import SuspiciousOperation
from operation.settings import SSO_CONFIG, OPERATION_CONFIG
from utils.oauth import Oauth


class OauthMiddleware(MiddlewareMixin):
    """
    第一步：用户第一次访问后台时，不带cookie，所以重定向至认证服务器进行认证
    第二步：认证服务器重定向至原始访问页面，不带cookie，但带有code
    第三步：后台使用code从认证服务器获取token，获取token之后再获取用户信息，在响应中设置cookie
    第四步：用户携带cookie访问后台，这时可以直接登录
    """

    def __init__(self, get_response=None):
        self.get_response = get_response
        engine = import_module(settings.SESSION_ENGINE)
        self.SessionStore = engine.SessionStore
        self.oauth = Oauth()

    def process_request(self, request):
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME)
        if session_key:
            request.session = self.SessionStore(session_key)
            return None
        session = self.SessionStore(session_key)
        client_id = SSO_CONFIG.get("CLIENT_ID", "")
        host = OPERATION_CONFIG.get("HOST", "")
        port = OPERATION_CONFIG.get("PORT", "")
        redirect_uri = "http://%s:%s%s" % (host, port, request.path)
        code = request.GET.get("code", None)
        if code:
            data = self.oauth.get_token(redirect_uri, code)
            if data is not None:
                access_token = data.get("access_token", "")
                session["access_token"] = access_token
                session['redirect_uri'] = redirect_uri
                if access_token:
                    user = self.oauth.get_profile(access_token)
                request.session = session
            return None
        params = {"client_id": client_id, "response_type": "code", "redirect_uri": redirect_uri}
        params = urllib.urlencode(params)
        url = SSO_CONFIG.get("URL", "") + "/oauth/authorize?"
        return HttpResponseRedirect(url + params)

    def process_response(self, request, response):
        """
        If request.session was modified, or if the configuration is to save the
        session every time, save the changes and set a session cookie or delete
        the session cookie if the session has been emptied.
        """
        try:
            accessed = request.session.accessed
            modified = request.session.modified
            empty = request.session.is_empty()
        except AttributeError:
            pass
        else:
            # First check if we need to delete this cookie.
            # The session should be deleted only if the session is entirely empty
            if settings.SESSION_COOKIE_NAME in request.COOKIES and empty:
                response.delete_cookie(
                    settings.SESSION_COOKIE_NAME,
                    path=settings.SESSION_COOKIE_PATH,
                    domain=settings.SESSION_COOKIE_DOMAIN,
                )
            else:
                if accessed:
                    patch_vary_headers(response, ('Cookie',))
                if (modified or settings.SESSION_SAVE_EVERY_REQUEST) and not empty:
                    if request.session.get_expire_at_browser_close():
                        max_age = None
                        expires = None
                    else:
                        max_age = request.session.get_expiry_age()
                        expires_time = time.time() + max_age
                        expires = cookie_date(expires_time)
                    # Save the session data and refresh the client cookie.
                    # Skip session save for 500 responses, refs #3881.
                    if response.status_code != 500:
                        try:
                            request.session.save()
                        except UpdateError:
                            raise SuspiciousOperation(
                                "The request's session was deleted before the "
                                "request completed. The user may have logged "
                                "out in a concurrent request, for example."
                            )
                        response.set_cookie(
                            settings.SESSION_COOKIE_NAME,
                            request.session.session_key, max_age=max_age,
                            expires=expires, domain=settings.SESSION_COOKIE_DOMAIN,
                            path=settings.SESSION_COOKIE_PATH,
                            secure=settings.SESSION_COOKIE_SECURE or None,
                            httponly=settings.SESSION_COOKIE_HTTPONLY or None,
                        )
        return response

```



