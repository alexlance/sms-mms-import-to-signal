
# SMS and MMS import into Signal Messenger

Turns out it's really hard to import your old MMS messages into Signal - this process
will help you merge in your SMS, MMS and Signal messages together in Signal, so you can
delete your Android system SMS/MMS store and make Signal your default SMS app without
losing any of your SMS and MMS history.

The script in this repo takes an XML file - as generated by SMS Backup and Restore (by
SyncTech), and uses https://github.com/bepaald/signalbackup-tools to dissect a normal
Signal backup file, and re-construct it again with all the messages from the
SMS Backup and Restore XML file imported.

## Caveats

 * Only tested on Android
 * The commands below were run on Linux
 * Some MMSes that were audio only, don't seem to make it across
 * As long as you backup Signal before you begin and you have written down the 30 digit passphrase, and you still have your phone number for identity verification, and you still remember your Signal Pin, then it shouldn't be too hard to get things back to normal if this procedure doesn't work out for you

## Instructions

1. Generate a Signal backup file

```
Signal -> Chats -> Backups -> Local Backup
```

2. Transfer that file to your computer, file will be named eg: signal-2022-06-10-17-00-00.backup

3. Run signalback-tools over the Signal backup file, eg:

```
  # Install signalbackup-tools
  git clone https://github.com/bepaald/signalbackup-tools ... (follow their directions for installation)

  # signal 30 digit backup passphrase
  key=123451234512345123451234512345

  # create a directory to store the dissected Signal backup
  mkdir bits

  # use signalbackup-tools to dissect the Signal backup
  ./signalbackup-tools ./signal-2022-06-10-17-00-00.backup ${key} --output bits/

```

4. Generate an SMS Backup and Restore (SyncTech) XML file on your phone and transfer that to a computer. Ensure the backup includes SMS and MMS, if that's what you want to import into Signal.

5. Run the script in this repo over the XML file:

```
python3 sms-mms-import-to-signal.py sms-20220611130220.xml bits/
```

6. Use signalbackup-tools to wrap up the new Signal backup file:

```
# note: file suffix of signal backup files *must* be ".backup"
signalbackup-tools bits/ --output ./signal.backup --opassword ${key}
```

7. Transfer the signal.backup file back to your phone

8. Delete Signal from your phone (only if you're 100% confident that you can use your original backup if things don't work!)

9. Reinstall Signal, it'll ask you if you've got a backup file that you want to restore from. You
will need:

a) The 30 digit passcode
b) A phone number that you can receive their validation code on
c) The Signal PIN number that you may have setup

10. Open Signal, you should see all your SMS messages and MMS messages, as well as your normal Signal messages, all in Signal now.

11. Once you're 100% certain everything looks great, my next steps were to use SMS Backup and Restore -> Tools section to erase all SMS/MMS messages, and change the default SMS messaging app over to Signal.


## Thoughts
* Feel free to shout out with any issues problems in github issues
* Make sure to go and give signalbackup-tools some kudos as they do most of the heavy lifting
* Wonder if we could move whatsapp messages using a similar procedure... ?
