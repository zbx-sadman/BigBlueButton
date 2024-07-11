# BigBlueButton tool set
This tools helps to monitoring [BigBlueButon](https://github.com/bigbluebutton/bigbluebutton) with [Zabbix](https://www.zabbix.com).

Graph examples

![Summary traffic / participants](https://user-images.githubusercontent.com/12827470/109484184-30a68c80-7a91-11eb-99c3-8bcaf19e11be.png)

![Total number of participants](https://user-images.githubusercontent.com/12827470/109484442-7cf1cc80-7a91-11eb-9d3b-df1b7f5825e0.png)


## BBB integration/template for Zabbix
Tested on:

- Production mode: BigBlueButton v2.7, Zabbix 6.0.14.

Metrics supported:
- Meetings: total count, running count, used as breakout room, meetings with joined participants & recording possibility;
- Participants: total count, moderators count, listeners & speakers & broadcasters;
- Recordings: total count, processing, processed, published, unpublished, deleted; size of raw & published recordings.

LLD available for:
- Meetings. All individual metrics will placed to the application which contains name of the meeting.

Clarification of the roles of users:
- Participants / viewers. Peoples who connected to meeting, but not connected to audio stream (have no microphone or headphones). They can chat, respond to polls, view presentation, display an emoji (such as their raised hand), and participate in a breakout sessions;
- Listeners. Participants who connected to audio stream in the "listen only" mode. They can do everything a viewers can, plus listen other participants, who have mic;
- Speakers. Participants who connect to audio stream with using voice feature. They can do everything a listeners can, plus talk to other participants thru microphone;
- Broadcasters. Participants who uses Webcam. They can do everything a viewers can, plus broadcast video stream to other participants;
- Moderators. Superusers in the conference. They can do everything a participants can, and something else. A moderator can mute/unmute other speakers, lock down viewers (such as restrict them from using private chat), and make anyone the current presenter. 

### How to use with Zabbix
#### 1. Fast

1. Add string ``UserParameter=bbb.miner[*],curl -s https://bbb.example.com/bigbluebutton/api/$1?checksum=`echo -n "$1BigBlueButtonSecretStringThere" | sha1sum |  head -c 40` 2>&1`` to the end of your ``zabbix_agentd.conf`` file;
2. Replace **bbb.example.com** with your BBB server hostname which used for the SSL-certificate
3. Replace **BigBlueButtonSecretStringThere** with your BBB server secret. You can get it from ``bbb-conf --secret`` command output;
4. Restart Zabbix agent;
5. Import [template](https://github.com/zbx-sadman/BigBlueButton/tree/master/Zabbix_Templates) to the Zabbix Server;
6. Link template to the BBB host with Zabbix web-interface;
7. Enjoy.

#### 2. Using host-level macroses (credits [@felcaetano](https://github.com/felcaetano))

1. Add string ``UserParameter=bbb.miner[*],curl -s https://$1/bigbluebutton/api/$3?checksum=`echo -n "$3$2" | sha1sum | head -c 40` 2>&1`` to the end of your ``zabbix_agentd.conf`` file;
2. Restart Zabbix agent;
3. Import [macrosed template](https://github.com/zbx-sadman/BigBlueButton/tree/master/Zabbix_Templates) to the Zabbix Server;
4. Link template to the BBB host with Zabbix web-interface;
5. Change values of the host-level macroses {$BBB_HOSTNAME} and {$BBB_SECRET} to your own;
7. Enjoy again.

**Hint**: All LLD-generated meetings's Applications will be stored for some time which specified in the {$KEEP_MEETING_GENERATED_ITEMS} macro, which default value is 7 days. If you need more history data - just create that macro on host level and set it value to... 25 years, for example.

**Beware**: BBB API's response can be big - ~65kb on _getMeetings_ request to the BBB that serves 4 meeting with 50 participants per each. Do not call frequently Template's 'master items' update.
