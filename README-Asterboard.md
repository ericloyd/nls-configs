There are some minor steps needed to install Asterboard and there are
some assumptions as to how your data is logged.

*** ASSUMPTIONS

* Your data is sent from one or more Asterisk servers running v 1.8 or higher.

* Your data is tagged from Asterisk as being the "asterisk" program (this is
        the "-t" parameter from the setup-linux.sh script).

* Asterisk uses "from-trunk" context for all incoming calls and "from-internal"
        for all outgoing calls (or at least, the final context before sending
        calls out goes through from-internal).


*** INSTALLING

Note: Older (pre 1.4.0) versions of NLS used a different directory for logstash
patterns.  If the directory in step 1a does not exist, use step 1b.  You do not
need to perform both steps.

1a) Copy "asterisk" to logstash's patterns directory:
cp asterisk /usr/local/nagioslogserver/logstash/vendor/bundle/jruby/1.9/gems/logstash-patterns-core-0.1.10/patterns

1b) Copy "asterisk" to older logstash patterns directory:
cp asterisk /usr/local/nagioslogserver/logstash/patterns


2) Log in to Nagios Log Server and go to "Administration"
        Go to "Global Configuration"
        Add a filter called "Asterisk" with the following content:

        if [program] == 'asterisk' {
          grok {
            match => ['message', '%{ASTLOG}']
          }
        }

        Save and Apply your new global configuration

3) Install the dashboard itself by going to the "Dashboard" screen
        Click the "Load" icon near the top right
        Select "Advanced" from the bottom of the pop-up
        Choose "Browse" and browse to where you saved the asterboard.json file

You can now save your new dashboard as whatever you want, but as it is primarily
showing "Concurrent Calls" we use "Concurrent Calls."

New Asterisk log data will now be parsed into the following fields:

        asterisk_app            Set, Macro, Dial, etc
        asterisk_app_params     Parameters to above
        asterisk_channel        SIP/1234@context (for instance)
        asterisk_context        1234@from-intenal:1 or 1234@from-trunk:1
        asterisk_ext            1234
        asterisk_extension      Internal extension used in transfers, etc
        asterisk_sip_channel    SIP/1234
        asterisk_src_file       pbx.c for outbound calls

These terms are also available for general Asterisk log file searching/analysis
though some are not used or useful for this particular dashboard.

Note that if you added Asterisk data prior to loading the global filter, that
data will not have the appropriate terms parsed and will not show up in the
call details, but they will show up in the overall counts.  Only new data added
after applying the global filter will show phone numbers and trunk information.


Feel free to contact me with questions:

Author: Eric Loyd <eric@bitnetix.com>
Copyleft 2014 by Eric Loyd
Last updated 23-Mar-2016
