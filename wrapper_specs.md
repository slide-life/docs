API wrapper specs

Actor
-----
1.  #fromObject(obj), .toObject(),
2.  #registerUser(identifier, password), -- registers the user as well as creating it
3.  #registerActor(), -- registers a new actor-user without an identifier or password for temp use, doesn’t use LocalStorage
4.  #get(identifier), -- loads public attributes of other user
5.  .decrypt(data), .encrypt(data),
6.  .get(identifier) -- loads public and private attributes of other user that I have access to
7. .getRelationships(), .loadRelationship(otherUser), -- loadRelationship gets or creates if not
8. .request(downstream, fields) -- loads relationship, creates conversation, then requests

User < Actor
------------
1.  ._serializeProfile(), ._deserializeProfile(),
2.  .getPublicProfile(), .getPrivateProfile(), .getProfile(),
    -- getPrivateProfile also decrypts profile
    -- getProfile returns entire thing if priv
    -- also if have the other user copied in my own profile, get its entire profile
3. .patchPublicProfile(fields), .patchPrivateProfile(plainFields),
4. .getSchema(), -- gets schema associated with this user
5. .getIdentifiers(), .addIdentifier(identifier),
6. .getDevices(), .addDevice(device),

Vendor
------
1.  .getForms(), .sendForm(fields), -- for users that are vendors, create a form and propagate it to all relationships using request

Relationship
------------
1. #fromObject(obj), .toObject(),
2. #create(obj),
3. #get(relationshipId),
4. .decrypt(data), .encrypt(data),
5. .getListeners(), .addListener(listenerParams), .listenWebSocket(onMessage),
	-- listenerParams instead of just a listener, because listener belongs_to relationship.
6. .getConversations(), .createConversation(description), .createConversationFromForm(description, form) -- form optional

Conversation
------------
1. #fromObject(obj), .toObject(),
2. #get(conversationId),
3. ._encryptAndSendMessage(kind, plainFields),
4. ._sendMessage(kind, fields),
5. .request(fields),
6. .deposit(fields),
7. .respond(fields)

Block -> Schema, but otherwise as is. Does
Form, Device, Listener, etc. -- constructors are main constructor (new), which is only used internally, and #create(..., cb)



