encrsync
========

Encrypted offsite backups, using encfs --reverse and rsync
encrsync supports the complete rsync option syntax.
So simply change the binary in your commands from rsync to encrsync to switch to remote encryption.

Usage
-----

    $ ./encrsync --help
    USAGE
	  Backup : encrsync [OPTION...] SRC [USER@]HOST:DEST
	  Restore: encrsync [OPTION...] [USER@]HOST:SRC [DEST]

    HINT 1	this script must be run as root to mount encfs encryption

    HINT 2	Please mind that only one source directory can be sync'ed

    HINT 3	After changing your keyfile/passfile please assure to sync by rsync option --checksum at least once!

    OPTIONS SUMMARY
	  --keyfile     encfs keyfile to use, default ~/.encfs6-encrsync.xml
		    	        File is created if not existing
	  --passfile	  file containing encfs keyfile password, default ~/.encfs-password
			            File is created if not existing
	  --no-snapshot	Create no snapshot after running a backup, default: create snapshot
	  -<rsync-opt...>	Every option of your rsync binary. Run rsync --help for a complete listing.


Example
-------

    $ ./encrsync -azHAX -e 'ssh -oStrictHostKeyChecking=no' --delete --numeric-ids . userX@userX.trustedspace.de:

The first run will generate a random password, save it to 'passfile' and request you to note down the password.
Encfs 'keyfile' is also created (without filename encryption). Further runs will not require any user input.


Backing up keyfile and password
-------------------------------

**Remember to back up the keyfile and the password**!
The keyfile is transferred as part of each sync in cleartext (it contains the encoded key,
so this is a minor security degradation).
Anyway it is recommended to backup both, the keyfile and the auto-generated password.
If you loose the password you will loose access to the encrypted data.
It is a very good idea to have multiple backups of the keyfile and password. For maximum
security, keep the data, the keyfile and the password at different locations.

After an intentional change/regeneration of keyfile and password please assure to
a) run encrsync with the rsync-parameter --checksum to assure every file is being
   transferred newly encrypted
b) or change or cleanup the backup location to avoid rsync to consider the old encryption as up-to-date

Running from crontab
--------------------

You most likely want to run encrsync from crontab. I recommend using flock or
something similar to make sure no multiple instances are running, like this:

    30 0 * * * flock -n /tmp/encrsync.lockfile /path/to/encrsync -azHAX -e 'ssh -oStrictHostKeyChecking=no' --delete --numeric-ids --bwlimit 100 $HOME/docs userX@userX.trustedspace.de:

Dependencies
------------

* encfs 1.7.4 or later
* python
* rsync

License
-------

encrsync is licensed under GPLv3, see http://www.gnu.org/licenses/gpl-3.0.html

encrsync is derived from encrb, written by
Copyright (c) 2012-2014 Heikki Hokkanen <hoxu at users.sf.net>
see https://github.com/hoxu/encrb/
