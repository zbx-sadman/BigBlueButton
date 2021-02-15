# BigBlueButton tool set
This tools helps to monitoring BigBlueButon with Zabbix.

## BBB integration/template for Zabbix
Tested on:

- Production mode: BigBlueButton v2.2, Zabbix 4.2.5.

Metrics supported:
- Meetings: total count, running count, used as breakout room, meetings with joined participants & recording possibility;
- Participants: total count, moderators count, listeners & participants with enabled voice/video feature.

Сlarification of the roles of users:
- Participants / viewers. Peoples who connected to meeting, but not connected to audio stream (have no microphone or headphones). They can chat, respond to polls, view presentation, display an emoji (such as their raised hand), and participate in a breakout sessions;
- Listeners. Participants who connected to audio stream in the "listen only" mode. They can do everything a viewers can, plus listen other participants, who have mic;
- Speakers. Participants who connect to audio stream with using voice feature. They can do everything a listeners can, plus talk to other participants thru microphone;
- Broadcasters. Participants who uses Webcam. They can do everything a viewers can, plus broadcast video stream to other participants;
- Moderators. Superusers in the conference. They can do everything a participants can, and something else. A moderator can mute/unmute other viewers, lock down viewers (such as restrict them from using private chat), and make anyone the current presenter. 

### How to use with Zabbix
1. Add string ``UserParameter=bbb.miner[*],curl -s https://bbb.example.com/bigbluebutton/api/$1?checksum=`echo -n "$1BigBlueButtonSecretStringThere" | sha1sum |  head -c 40` 2>&1`` to the end of your ``zabbix_agentd.conf`` file;
2. Replace **bbb.example.com** with your BBB server hostname which used for the SSL-certificate
3. Replace **BigBlueButtonSecretStringThere** with your BBB server secret. You can get it from ``bbb-conf --secret`` command output;
4. Restart Zabbix agent;
4. Import [template](https://github.com/zbx-sadman/BigBlueButton/tree/master/Zabbix_Templates) to the Zabbix Server;
6. Enjoy.

**Beware**: BBB API's response can be big - ~65kb on _getMeetings_ request to the BBB that serves 4 meeting with 50 participants per each. Do not set Template's 'master items' update interval so small.
