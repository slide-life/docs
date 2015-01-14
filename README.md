# Actors

## Introduction

An actor represents an upstream entity that must receive a user's data. A conversation has a downstream user sending data and an actor. The conversation model handles all data pushing and pulling, so it can send data to both the user's own computer and to the vendor. This new model also handles updates to details, because conversations are ongoing and users can push data upstream at any time. (Channels have been replaced by conversations and actors.)

Cryptography has now been revamped to use AES-CBC, because it is more standard and it supports greater ciphertext lengths. Because of this, every user has an RSA public key, and all conversations have a symmetric key encrypted using the public key of the user.

## Flows

-> is a direct call

--> is a callback, intent, or response to WS

###Requesting details:

    (JS) Actor.openRequest -> (JS) Actor.openConversation -> (POST) /actors -> ...
    .. -> (JS) Actor.listen -> (WS) /actors/:id/listen (if populating on user computer: --> callback (JS) Slide.populateFields)
    .. -> (JS) Conversation.request -> (POST) /conversations/:id/request_content -> (Ruby) NotificationJob.perform -> (Ruby) (currently) APN.push (future addition) Device.push

###Response to device push:

    (iOS) AppDelegate#application:didReceiveRemoteNotification: -> (Notification) notification
    .. Notification callback added in RequestsViewController#viewDidLoad
    .. --> RequestsViewController#getData: -> wait for user to initiate request
    (Android) (GCM) -> GcmIntentService.onHandleIntent -> (Intent) REQUEST_INTENT --> (BroadcastReceiver) RequestsFragment.recv.onReceive ; then wait for user to initiate request

###User pushing details to QR-coded actor:

    (iOS) (QR) -> QRReaderViewController -> QRReaderViewController#queryBackend -> QRReaderViewController#showForm -> RequestsViewController#addRequest: ; then wait for user to initiate request
    (Android) (QR) -> QRFragment -> QRFragment.onActivityResult -> (Intent) REQUEST_INTENT --> (BroadcastReceiver) RequestsFragment.recv.onReceive ; then wait for user to initiate request

###User sending or updating details on phone:

    (iOS) RequestViewController -> RequestViewController#confirm -> API#postPayload:forConversation:onSuccess: -> (PUT) /conversations/:id -> (backend)
    (Android) RequestActivity -> RequestActivity.initializeSubmit -> API.postData -> API.postBooleanValue to getConversationPath(request.conversationId) -> (PUT) /conversations/:id -> (backend)
    (backend) Conversation.upstream! -> Actor.stream -> Actor.notify -> (WS) /actors/:id/listen
