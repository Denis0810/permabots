============
Installation
============

At the command line::

    $ pip install permabots    
    
Add ``permabots`` to your ``INSTALLED_APPS``, and run::

	$ python manage.py migrate permabots
	
Instant messaging providers uses webhooks to send messages to your bots. Add permabots processing urls to your urlpatterns::

	url(r'^processing/', include('permabots.urls_processing', namespace="permabots"))
	
Webhooks are generated with ``django.contrib.sites``. You need it installed and ``SITE_ID`` configured. If you want to generate webhook manually you can do it::

	MICROBOT_WEBHOOK_DOMAIN = 'https://yourdomain.herokuapp.com' 

.. tip::

	It is usefull when you don't have ``https`` in your public domain but you have it in your autogenerated domain. i.e. heroku.
	
Bots are associated to Django Users. You need at least one user, for example the admin user.

Demo
---------

You can check or deploy this Permabots demo https://github.com/jlmadurga/permabots-demo

.. image:: https://www.herokucdn.com/deploy/button.svg
    :target: https://heroku.com/deploy?template=https://github.com/jlmadurga/permabots-demo/tree/master


REST API
-----------

Permabots comes with a REST API to manage all elements. If you want to use REST API *(recommended)* permabots requires ``rest_framework`` and ``rest_framework.authtoken``. Added them to your ``INSTALLED_APPS``. 

And then configure urls::

	url(r'^api/v1/', include('permabots.urls_api', namespace="api"))		
	

API uses Token authentication. Generate token when user is created::

	@receiver(post_save, sender=settings.AUTH_USER_MODEL)
	def create_auth_token(sender, instance=None, created=False, **kwargs):
    	if created:
        	Token.objects.create(user=instance)
        	
        	
.. note::

	You can check API in official Permabots.com_ implementation.
	
	.. _Permabots.com: http://www.permabots.com/docs/api


Celery
--------------

Permabots uses celery to delegate processing to workers and release webhooks. Permabots do not require any special settings for celery. 


.. tip::

	Just remember using DB as broker will slow down the processing. Do not used in order to avoid delay in bot replays. 

Cache
-----------
Cache is extensively used by Permabots. Set your preferred cache backend to reduce processing time.

.. note::

	Once a chat bot is configured most of their models are static ``handlers``, ``templates``, etc so it is not required to access
	DB each message arrives to permabots
