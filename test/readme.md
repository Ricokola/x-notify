


# Test

Brief list of what to create test for

* Subscribe to a topic
	- source: restFull API
	- db: email added in exist subs collection
	- db: new entry in subsUnconfirmed

* Subscribe to a topic - invalid email or undefined topic id
	- source: restFull API
	- answer: JSON - status 400
	- note: Our example should cover this situation, like displaying a message, the form parameter is not properly configured. 

* Subscribe to a topic before the delay (25 min)
	- source: restFull API
	- db: logged
	- answer: JSON - status 200
	- note: In the confirmation message it should be mentionned to check in the spam box, and to whitelist the appropriate Notify sender email. Also it can mention that if he didn't received the confirmation, to try again later in half hours.

* Subscribe to a topic after the delay (25 min)
	- source: restFull API
	- db: logged
	- answer: JSON - status 200
	- notify: A new confirmation email is sent.

* Subscribe after subscription is completed
	- source: restFull API
	- db: logged
	- return: JSON - status 200

* Subscribe to unknown topic id
	- source: restFull API
	- db: logged
	- return: JSON - status 200

* Subscribe on internal error
	- source: restFull API
	- return: JSON - status 500



* Confirm the subscription
	- source: Email link
	- get redirect to a success page specific to the topic.
	- db: Subs data is moved into a subsConfirmed collection
	- db: unsub entry is deleted
	- db: subs logs created
	- answer: redirect

* Confirm the subscription after confirmed
	- source: Email link
	- answer: redirect to a general error page
	- note: That page can be a simple page not found or a message saying the link has expired. But it is impossible to know the difference.

* Confirm subscription with a wrong subscode
	- source: Email link
	- answer: redirect to a general error page



* Unsubscribe
	- source: Email link
	- db: logged
	- db: removed from Confirmed subscriber
	- db: added a record in unsub collection

	
* Notify Failling 
	- source: Notify API call
	- db: logged
	- impact: system will show as it was successful


## cURL command

Subscribe

```
curl -i -X POST -H 'Content-Type: application/json' -d '{"eml": "pierre@example.com", "tid": "test"}' http://127.0.0.1:8080/api/v0.1/subs/email/add
```

Confirm

```
curl -i -X GET http://127.0.0.1:8080/subs/confirm/2d3903975209cbq2/87462102
```


Unsubscribe

```
curl -i -X GET http://192.168.1.205:8080/subs/remove/2d3903975209cbq2/87462102
```

## database command

Create a topic

```
db.topics.insertOne( {
    _id: "test2",
    templateId: "<template id available in the template in Notify>",
    notifyKey: "<A valid Notify API key>",
    confirmURL: "https://canada.ca/en.html",
    unsubURL: "https://canada.ca/en.html"
})
```

Create a topic detail

```
db.topics_details.insertOne( {
    _id: "test2",
    accessCode: [ "123456" ],
	createdAt: ISODate( "2020-03-21T00:00:00.000-04:00" ),
	lastUpdated: ISODate( "2020-03-21T00:00:00.000-04:00" ),
	groupName: "Department Name",
	description: "Used for this service, related to request #",
	lang: "en",
	langAlt: [ "test" ],
	nServiceId: "test-serviceID-to-be-extracted-from-Notify-API-key"
})
```

Remove a user from the subs

```
db.topics.updateOne( 
	{ _id: "test" },
	{
		$pull: {
			subs: "<email....>"
		}
	});
```


## Creation of new topic

1. Create a minimal topic as per the Create a topic query above
2. Create a topic details collection
	* topic_id
	* groupName
	* accessCode: `[ "accessCode" ]`
	* list-retreived: `[ { time, accessCode } ]`
	* description
	* lang
	* langAlt: `[ "topicIds" ]`

## Mongo Indexes and schema

todo

## Endpoint URLs

* API end point: https://apps.canada.ca/x-notify/api/v0.1/subs/email/add
* Confirmation link: https://apps.canada.ca/x-notify/subs/confirm/
* Remove link: https://apps.canada.ca/x-notify/subs/remove/

* Get subscriber list: https://apps.canada.ca/x-notify/api/v0.1/t-manager/123456/test/topic/list