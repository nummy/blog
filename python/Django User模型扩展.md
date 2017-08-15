通常情况下，Django提供的User模型能够满足我们大部分的需求，但是有时候我们需要给User添加一些格外的功能和信息。

Django支持两种方式来扩展User模型。

- **代理模型** 如果只是需要添加一些功能性操作，可以基于User创建一个代理模型。
- **关联模型** 如果是需要添加格外的字段信息，则可以创建一个与User关联的模型，两者之间的关系为1对1。这个模型通常称之为profile model，因为大部分情况下这些格外信息都与认证无关。

### 关联模型

下面创建一个关联模型，给User添加手机信息。

```python
class Profile(models.Model):
    user = models.OneToOneField(User)
    mobile = models.CharField(max_length=15, verbose_name=u'手机号')

    class Meta:
        verbose_name = u'附加信息'
        verbose_name_plural = u'附加信息'
```

假设数据库中已经有一个用户同时具有User和Profile模型，则可以Django的关联模型获取数据：

```python
>>> u = User.objects.get(username='fsmith')
>>> mobile = u.profile.mobile 
```

为了在管理后台中将Profile作为一个字段添加到User管理页面中，需要定义一个	`InlineModelAdmin`， 并将它添加到UserAdmin类中。

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from django.contrib.auth.models import User
from .models import Profile

class ProfileInline(admin.StackedInline):
    model = Profile
    can_delete = False
    verbose_name_plural = '附加信息'

# Define a new User admin
class UserAdmin(UserAdmin):
    inlines = (ProfileInline,)

# Re-register UserAdmin
admin.site.unregister(User)
admin.site.register(User, UserAdmin)
```

这些profile模型并没有什么特别之处，它们只是与User模型存在一对一的关联而已。因此，当创建用户的时候，profile并不会自动创建，可以通过`django.db.models.signals.post_save`来创建或者更新profile模型。

```python
# coding:utf-8
from __future__ import unicode_literals
from django.db import models
from django.contrib.auth.models import User
from django.db.models.signals import post_save


# Create your models here.
class Profile(models.Model):
    user = models.OneToOneField(User)
    mobile = models.CharField(max_length=15, verbose_name=u'手机号')

    class Meta:
        verbose_name = u'附加信息'
        verbose_name_plural = u'附加信息'

    def save(self, *args, **kwargs):
        if not self.pk:
            try:
                p = Profile.objects.get(user=self.user)
                self.pk = p.pk
            except Profile.DoesNotExist:
                pass
        super(Profile, self).save(*args, **kwargs)


def create_user_profile(sender, instance, created, **kwargs):
    print(kwargs)
    if created:
        profile, created = Profile.objects.get_or_create(user=instance)


post_save.connect(create_user_profile, sender=User)
```

