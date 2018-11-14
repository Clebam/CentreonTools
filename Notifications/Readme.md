# Notify MS Teams #

## Prerequisites ##
- You need to have MS Teams :)
- You need to add "Incoming Webhook" to you channel
- You need to have the token (URL) generated

## Setting up a message card in json ##
### Card example for service notification ###
```json
{
    "@context": "https://schema.org/extensions",
    "@type": "MessageCard",
    "themeColor": "EA4300",
    "title": "<div>$HOSTGROUPNAME$ - Alerte - ETAT $SERVICESTATE$ du service $SERVICEDESC$ sur l'hôte $HOSTNAME$</div>",
    "summary": "Alerte Centreon",
    "sections": [
        {
            "activityTitle": "**$SERVICEOUTPUT$**"
        },
        {
            "facts": [
                {
                    "name": "Type de notification",
                    "value": "<div>$NOTIFICATIONTYPE$</div>"
                },
                {
                    "name": "Service",
                    "value": "<div>$SERVICEDESC$</div>"
                },
                {
                    "name": "Hôte",
                    "value": "<div>$HOSTNAME$</div>"
                },
                {
                    "name": "Adresse IP",
                    "value": "<div>$HOSTADDRESS$</div>"
                },
                {
                    "name": "Description Hôte",
                    "value": "<div>$HOSTALIAS$</div>"
                },
                {
                    "name": "Etat",
                    "value": "<div>$SERVICESTATE$</div>"
                },
                {
                    "name": "Horodatage",
                    "value": "<div>$DATE$ $TIME$</div>"
                }
            ]
        }
    ],
    "potentialAction": [
        {
            "@type": "OpenUri",
            "name": "Acquitter via Centreon",
            "targets": [
                {
                    "os": "default",
                    "uri": "https://mycentreonserver.xyz/centreon"
                }
            ]
        },
        {
            "@type": "OpenUri",
            "name": "Voir Wiki Hôte",
            "targets": [
                {
                    "os": "default",
                    "uri": "https://mycentreonserver.xyz/centreon/$HOSTNOTESURL$"
                }
            ]
        },
        {
            "@type": "OpenUri",
            "name": "Voir Wiki Service",
            "targets": [
                {
                    "os": "default",
                    "uri": "https://mycentreonserver.xyz/centreon/$SERVICENOTESURL$"
                }
            ]
        }
    ]
}
```

The `<div>` are used to escape markdown characters to be interpreted as markdown styling

### Flatten the json ###
- Go to http://jsonviewer.stack.hu/ and paste the json code
- Use __Remove white space__ to have a compressed format

### Escape special characters ###
- Replace `"` with `\"`
- There may be other characters to escape

## Include json in the command ##
- You have to include your url token here
```shell
/usr/bin/curl -H "Content-Type: application/json" -d "{JSON HERE}" https://outlook.office.com/webhook/...
```
- Paste the json in the right spot

```shell
/usr/bin/curl -H "Content-Type: application/json" -d "{\"@context\":\"https://schema.org/extensions\",\"@type\":\"MessageCard\",\"themeColor\":\"EA4300\",\"title\":\"<div>$HOSTGROUPNAME$ - Alerte - ETAT $SERVICESTATE$ du service $SERVICEDESC$ sur l'hôte $HOSTNAME$</div>\",\"summary\":\"Alerte Centreon\",\"sections\":[{\"activityTitle\":\"**$SERVICEOUTPUT$**\"},{\"facts\":[{\"name\":\"Type de notification\",\"value\":\"<div>$NOTIFICATIONTYPE$</div>\"},{\"name\":\"Service\",\"value\":\"<div>$SERVICEDESC$</div>\"},{\"name\":\"Hôte\",\"value\":\"<div>$HOSTNAME$</div>\"},{\"name\":\"Adresse IP\",\"value\":\"<div>$HOSTADDRESS$</div>\"},{\"name\":\"Description Hôte\",\"value\":\"<div>$HOSTALIAS$</div>\"},{\"name\":\"Etat\",\"value\":\"<div>$SERVICESTATE$</div>\"},{\"name\":\"Horodatage\",\"value\":\"<div>$DATE$ $TIME$</div>\"}]}],\"potentialAction\":[{\"@type\":\"OpenUri\",\"name\":\"Acquitter via Centreon\",\"targets\":[{\"os\":\"default\",\"uri\":\"https://mycentreonserver.xyz/centreon\"}]},{\"@type\":\"OpenUri\",\"name\":\"Voir Wiki Hôte\",\"targets\":[{\"os\":\"default\",\"uri\":\"https://mycentreonserver.xyz/centreon/$HOSTNOTESURL$\"}]},{\"@type\":\"OpenUri\",\"name\":\"Voir Wiki Service\",\"targets\":[{\"os\":\"default\",\"uri\":\"https://mycentreonserver.xyz/centreon/$SERVICENOTESURL$\"}]}]}" https://outlook.office.com/webhook/...
```

- Now you just have to create the notification command in centreon

# Notes #
- Right now, $SERVICENOTESURL$ is not working as intended, there is an opened issue here : https://github.com/centreon/centreon/issues/6612
- Feel free to improve it. I may study a way to use a json file to avoid all these conversion. (I did not try yet)