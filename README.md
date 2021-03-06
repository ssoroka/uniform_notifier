UniformNotifier
===============

uniform_notifier is extracted from [bullet][0], it gives you the ability to send notification through rails logger, customized logger, javascript alert, javascript console, growl, and xmpp.

Install
-------

### install directly

    gem install uniform_notifier

  if you want to notify by growl or xmpp, you should install them first

    gem install ruby-growl
    gem install xmpp4r

### add it into Gemfile (Bundler)

    gem "uniform_notifier"

  you should add ruby-growl and xmpp4r gem if you want.

Usage
-----

There are two types of notifications,
one is <code>inline_notify</code>, for javascript alert and javascript console notifiers, which returns a string and will be combined,
the other is <code>out_of_channel_notify</code>, for rails logger, customized logger, growl and xmpp, which doesn't return anything, just send the message to the notifiers.

By default, all notifiers are disabled, you should enable them first.

    # javascript alert
    UniformNotifier.alert = true

    # javascript console (Safari/Webkit browsers or Firefox w/Firebug installed)
    UniformNotifier.console = true

    # rails logger
    UniformNotifier.rails_logger = true

    # customized logger
    logger = File.open('notify.log', 'a+')
    logger.sync = true
    UniformNotifier.customized_logger = logger

    # growl without password
    UniformNotifier.growl = true
    # growl with passowrd
    UniformNotifier.growl = { :password => 'growl password' }

    # xmpp
    UniformNotifier.xmpp = { :account => 'sender_account@jabber.org',
                             :password => 'password_for_jabber',
                             :receiver => 'recipient_account@jabber.org',
                             :show_online_status => true }

After that, you can enjoy the notifiers, that's cool!

    # the notify message will be notified to rails logger, customized logger, growl or xmpp.
    UniformNotifier.active_notifiers.each do |notifier|
      notifier.out_of_channel_notify(args.join("\n"))
    end

    # the notify message will be wrapped by <script type="text/javascript">...</script>,
    # you should append the javascript_str at the bottom of http response body.
    # for more information, please check https://github.com/flyerhzm/bullet/blob/master/lib/bullet/rack.rb
    responses = []
    UniformNotifier.active_notifiers.each do |notifier|
      responses << notifier.inline_notify(notify_message)
    end
    javascript_str = responses.join("\n")

Growl Support
-------------

To get Growl support up-and-running, follow the steps below:

* Install the ruby-growl gem: <code>gem install ruby-growl</code>
* Open the Growl preference pane in Systems Preferences
* Click the "Network" tab
* Make sure both "Listen for incoming notifications" and "Allow remote application registration" are checked. *Note*: If you set a password, you will need to set <code>UniformNotifier.growl_password = { :password => 'growl password' }</code> in the config file.
* Restart Growl ("General" tab -> Stop Growl -> Start Growl)
* Boot up your application. UniformNotifier will automatically send a Growl notification when Growl is turned on. If you do not see it when your application loads, make sure it is enabled in your initializer and double-check the steps above.

### Ruby 1.9 issue

ruby-growl gem has an issue about md5 in ruby 1.9, if you use growl and ruby 1.9, check this [gist][1]

XMPP/Jabber Support
-------------------

To get XMPP support up-and-running, follow the steps below:

* Install the xmpp4r gem: <code>gem install xmpp4r</code>
* Make both the sender and the recipient account add each other as contacts.
  This will require you to manually log into both accounts, add each other
  as contact and confirm each others contact request.
* Boot up your application. UniformNotifier will automatically send an XMPP notification when XMPP is turned on.


[0]: https://github.com/flyerhzm/bullet
[1]: https://gist.github.com/300184
