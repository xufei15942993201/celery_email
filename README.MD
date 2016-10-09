在Django中怎么用Celery呢？当然第三篇中是一种方法，那种方法自由度很大，也很有用。不过，既然Django的Admin如此好用，我们有没有什么方式将Admin与Celery结合起来呢，下面就来介绍django-celery插件。

1.安装django-celery

django-celery是一个已经写好的Django App插件，我们使用pip方式安装。

$ sudo pip install django-celery

2.配置settings.py

添加配置至settings.py:

#使用的为上一篇的demo project
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'home',#上一篇自己的应用
    'djcelery',#加上这个东西，不要问我为什么
)
...
#末尾添加数据库调度
CELERYBEAT_SCHEDULER = 'djcelery.schedulers.DatabaseScheduler'

3.数据库

同步数据库，命令行输入：

$ python manage.py migrate

运行后，我们可以看到类似如下图：

选区_005.png


以为在数据库中添加相关djcelery的表。
4.初始命令

    在一个终端中重启Celery命令：

    $ celery -A demo worker -l debug

    打开另一个终端窗口，输入命令：

    $ celery -A demo beat -l debug --max-interval=10 #每十秒扫瞄任务
    上面命令意为每10秒钟扫描djcelery任务，不过现在我们还没有定义任何定时任务，请看第5步。
5.配置Admin后台

因为要用到Admin后台，我们必须新建超级用户：

$ python manage.py createsuperuser

然后按照提示一步步进行输入。

打开第三个终端，重启项目：

$ python manage.py runserver

进入后台：127.0.0.1:8000/admin/

会看到Djcelery应用下有如图下五项：

选区_006.png


怎样配置：

    定时。进入Crontabs页面，这个和Linux的Crontab命令类似，如不熟悉可自行google（因为我也不怎么用）。点击Add crontab按钮后输入14 保存（每到十四分钟时执行任务）。
    配置任务。进入Periodic tasks周期性任务选项。点击Add Periodic tasks按钮。

解释：我们设置了一个名为Hello的任务。任务内容为home.tasks.hello_world,也就是我们上一节中写的那个。选择周期为我们第一步定义好的14 。当然你也可以配置一下Interval。
6.查看结果

待到每个小时的14分钟，你便可以看到Celery的后台执行了hello_world函数（打印了hello world）。不过这个Crontab定得太特殊了。我们可以再写一个类似/5 *,每5分钟执行...














