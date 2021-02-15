# BigBlueButton tool set
This tools helps to monitor BigBlueButon with Zabbix.

## BBB integration/template for Zabbix
Tested on:

- Production mode: BigBlueButton v2.2, Zabbix 4.2.5.

Support metrics:
- Meetings: total count, running count, used as breakout room, meetings with participants & recording availability
- Participants: total count, moderators count, listeners & participants with enabled voice/video feature.

### How to use with Zabbix
1. Add string ``UserParameter=bbb.miner[*],curl -s https://**bbb.example.com**/bigbluebutton/api/$1?checksum=`echo -n "$1**D4ezgrASBnp56zSErp510lKNmfr314f8dubGntxzE**" | sha1sum |  head -c 40` 2>&1`` to the end of your ``zabbix_agentd.conf`` file;
2. Replace **bbb.example.com** with your BBB server hostname which used for the SSL-certificate
3. Replace **D4ezgrASBnp56zSErp510lKNmfr314f8dubGntxzE** with your BBB server secret. You can get it from ``bbb-conf --secret`` command output;
4. Restart Zabbix agent;
4. Import [template](https://github.com/zbx-sadman/BigBlueButton/tree/master/Zabbix_Templates) to the Zabbix Server;
6. Enjoy.

**Beware**: BBB API's response can be big - ~65kb on _getMeetings_ request to the BBB that serves 4 meeting with 50 participants per each. Do not set Template's 'master items' update interval so small.
