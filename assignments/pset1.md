# PSET 1

## Questions

### Two Invariants

1. The count of a Request must be a whole number, so not a negative number, nor a fraction, etc.
2. For any given Request, its corresponding set of Purchases must be of the same Item, so the Request's Item is the same as the Request's Purchase's Item.

#### Which invariant is more important?

The more important invariant is the first one I listed: the count of a Request must be a whole number. This is due to the fact that it directly impacts the User more. If people can overrequest an item, someone's wedding could end up with 72 toothbrushes but not anything else of use! However, for the other one, it would be confusing, but ultimately these still should be related to one another as the set of Purchases will still belong to the Request.

#### Which action is most impacted by this invariant and how does it maintain it?

The action "purchase" is most affected by this invariant, as it ensures that you cannot purchase more than what is requested, which is the main way the count of a Request could possibly go below zero, as when you purchase something it causes the Request's count Number to drop by whatever amount you purchased. Furthermore, it maintains this invariant by *requiring* that when one purchases an item, there must be "a request for this item with at least count", so that your count cannot be higher than what is requested, and thus you can't make it go below zero.

### Fixing An Action

Yes, "addItem" can potentially break this invariant. When you add an Item, all it requires is that the registry exists. However, it doesn't specify what the count must be. When the owner sets up their registry, they could add an item and request -2.5 of an item, which breaks the invariant, as the count of the Request is no longer a natural number. The problem could be fixed by *requiring* that the count must be a natural number (i.e. it cannot be negative, 0, or a decimal).

### Inferring behavior

All that the open action requires is that the registry exists and is currently marked as not active. All the close action does is make the registry not active, but it doesn't delete it or anything. This means that a registry could, indeed, be opened, closed, then opened again. Furthermore, this would even be a good thing! Imagine someone has a baby shower, they create a registry with all the supplies they would need for a baby, people purchase some (but not all) of the items, and then they reach close to the date of their baby shower, and so they mark it as closed. Suddenly, a relative that had previously said they couldn't come announces that they can, in fact, be there, and wants to purchase a gift. Instead of having to create a totally new registry with all the same items and counts of the other registry, they just reopen this registry. It's much easier for the user.

### Registry deletion

No. Deleting a registry wouldn't really change anything, since when someone is done with their registry, they can simply mark it inactive and leave it in their account. Deleting it doesn't really affect anything for the user, because nobody can interact with it anyhow.

### Queries

1. Registry owner: a registry owner request to see a list of the items (and how many of them) that have been purchased.
2. Giver of a gift: A gift giver request a specific item to see how many of them are left to purchase.

### Hiding purchases

I would change it so that the set of Requests's count Number does not decrease with every purchase of some amount. This way, the Request's amount left (count) is not seen by the registry owner. Instead, when purchasing, it'll only allow a purchase so that an item's sum of the set of Purchase's count + the purchase's count occurring right now is less than the Request's count Number.

**purchase** (purchaser: User, registry: Registry, item: Item, countCurrent: Number)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** registry exists, is active and has a request for this item *so that the sum of counts for this item + countCurrent <= the Request's count Number*.

&nbsp;&nbsp;&nbsp;&nbsp;**effects** create a new purchase for this purchaser, item, and count. Does NOT decrease the Request's count number.

### Generic types

Using generic types for item makes it much easier to describe and DRYs (don't repeat yourself) out the code. For each action above, you don't have to say stuff like purchase(item: Apple | Bagel | Pacifier | Cake | etc..) to show what can be purchased and what can't. It makes the code much easier to use and ready for change, because if you need to add/remove something to the store's inventory you don't have to go back through all this code and add/remove to this code to do so.

---

## Exercise 2

### Complete the definition of the concept state and write a requires/effects specification for each of the two actions

**concept** PasswordAuthentication

**purpose** limit access to known users

**principle** after a user registers with a username and a password,
they can authenticate with that same username and password
and be treated each time as the same user

**state**

a set of Users with

&nbsp;&nbsp;&nbsp;&nbsp;a username String

&nbsp;&nbsp;&nbsp;&nbsp;a password String

**actions**

**register** (username: String, password: String): (user: User)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** the username does not exist

&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a new User with the username username and password password, adds it to the set of Users, then returns it

**authenticate** (username: String, password: String): (user: User)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** requires the username to exist in the set of Users and for said user to have a matching username and password

&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns the User associated with the username and password

### What essential invariant must hold on the state? How is it preserved?

All usernames are unique within the set of Users. It is preserved because when one registers, it requires the username to not exist previously, thus the new username must be different from all previous ones.

### One widely used extension of this concept requires that registration be confirmed by email. Extend the concept to include this functionality

**concept** PasswordAuthentication

**purpose** limit access to known users

**principle** after a user registers with a username and a password,
they can authenticate with that same username and password
and be treated each time as the same user

**state**

a set of Users with

&nbsp;&nbsp;&nbsp;&nbsp;a username String

&nbsp;&nbsp;&nbsp;&nbsp;a password String

&nbsp;&nbsp;&nbsp;&nbsp;a token Number

&nbsp;&nbsp;&nbsp;&nbsp;an emailConfirmed Boolean

**actions**

**register** (username: String, password: String, email: String): (user: User, token: Number)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** the username does not exist

&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a new User with the username username, password password, emailConfirmed boolean False, and token token and adds it to the set of Users. Then, it emails the email provided a special token. Finally, it returns the user created.

**confirm** (username: String, token: Number)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** the username to exist in the set of Users and for the token to match the token associated with said User.

&nbsp;&nbsp;&nbsp;&nbsp;**effects** this sets the emailConfirmed Boolean to True.

**authenticate** (username: String, password: String): (user: User)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** requires the username to exist in the set of Users, for said user to have a matching username and password, and for the emailConfirmed Boolean to be true.

&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns the User associated with the username and password

---

## Exercise 3

### Specification for PersonalAccessToken

**concept** PersonalAccessToken

**purpose** allow people with a token to access the token holder's resouces with limited permissions

**principle** after a user with an account has created a Personal Access Token, anyone with this Personal Access Token can access the user's resources but with limited permissions (i.e. not "owner" status)

**state**

a set of Users with

&nbsp;&nbsp;&nbsp;&nbsp;a token Token

a Token with

&nbsp;&nbsp;&nbsp;&nbsp;a tokenString String

&nbsp;&nbsp;&nbsp;&nbsp;a permission Permission

**actions**

**createToken** (user: User, permission: Permission): (token: Token)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** user to be in the set of Users

&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a token by generating a tokenString, then coupling it with the permissions set for the token. Then, it returns the token.

**authenticate** (username: String, tokenString: String): (user: User, token: Token)

&nbsp;&nbsp;&nbsp;&nbsp;**requires** the username to be associated with one of the Users in the set of Users and the tokenString to be associated with said User

&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns access to the User's resources with token's permissions

### How PersonalAccessToken differs from PasswordAuthentication

A PersonalAccessToken differs from PasswordAuthentication because a PersonalAccessToken is used for people to access a user's account and resources but only with a limited set of permissions or scope, like having 'read-only' permissions and not being allowed to edit any of the files. On the other hand, a password is meant for the user of the account to gain access to *all* of their account, without it being locked behind any extra features. After all, there shouldn't be anyone with higher permissions than the owner of the account, who should have the password (and ideally, be the *only* one to have the password).
It's almost like having a secret painting you're working on, having a ring of keys that you attach to your belt that allows you to access the drawing utensils, paints, box that it is locked in, etc. The ring of keys here is the password, but you can also copy certain keys and hand them to people you trust to do things with them, which is the Personal Access Token. Like, if you want to show your friends, you'll give them the a copy of the key to the box it's kept in, but you won't give them the key to the paints, because they're rather clumsy and you don't trust them *that* much.

### How the Github Documentation could be improved

Put a much higher emphasis on what the token actually does and is meant to be used for than a short blurb. Until I looked very closely at what it said, I was under the impression that these had no difference, after all, they're both ways to access an account. Maybe a section that shows how multiple people or even algorithms can have this token, then access to the account, but not be able to do things the owner forbids.

---

## Exercise 4

### Conference Room Booking

**concept** ConferenceRoomBooker

**purpose** to ensure conference rooms are available at the time the person who books it will use it

**principle** the room controller lists the rooms, dates, and times available. The booker selects the room they want, the date they want, and the start time they want, then when they get to the room at that time and date, their room is ready for them and will continue to be empty until the end time.

**state**

a set of Rooms with

&nbsp;&nbsp;&nbsp;&nbsp; a set of Bookings

&nbsp;&nbsp;&nbsp;&nbsp; a set of available Slots

a Bookings with

&nbsp;&nbsp;&nbsp;&nbsp; a User

&nbsp;&nbsp;&nbsp;&nbsp; a set of Slots

a Slot with

&nbsp;&nbsp;&nbsp;&nbsp; a Date

&nbsp;&nbsp;&nbsp;&nbsp; a Time

**actions**

**book** (user: User, room: Room, date: Date, start: Time, endInclusive: Time): (booking: Booking)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** for the date and time to match with a slot/slots in the set of slots in the selected room. The date and time to not occur in the past. The start must be a time before the endInclusive.

&nbsp;&nbsp;&nbsp;&nbsp; **effects** creates a Booking associated with the user and creates a new Slot, associated with the date and start Time, for each Time between start and endInclusive, including endInclusive. It associates these slots with the booking, adds this booking to the room's set of Bookings. Then, it removes the matching slot(s) from the room's set of available slots, so that nobody can select them again. Finally, it returns the booking. (see additional notes if slots are confusing)

**cancel** (user: User, booking: Booking, room: Room)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** for the booking to be associated with the *user* and for the room to be associated with the booking

&nbsp;&nbsp;&nbsp;&nbsp; **effects** removes the booking from the room's set of Bookings then adds the slot(s) associated with the booking to the room's set of available slots.

**addSlot**(room: Room, start: Time, date: Date): (slot: Slot)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** there to *not* already be this start Time and date already associated with a slot that's already associated with this room

&nbsp;&nbsp;&nbsp;&nbsp; **effects** creates a new slot, associates it with this time and date, then adds the newly-created slot to the room's list of available slots, and finally returns the slot.

**deleteSlot**(slot: Slot, room: Room)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** for the slot to not already be associated with a Booking in the room

&nbsp;&nbsp;&nbsp;&nbsp; **effects** removes the slot from the Room's available Slots

**additional notes**

I see no reason to punish no-shows, where someone books but doesn't show up, just because I think sometimes slots are created for longer than someone would need. For instance, if slots are created to be more than an hour long, but someone just needs a 20 minute window to have a quick check-in with partners, I think it's alright if they don't stay the whole time, or even if they show up late. They booked the room, it's theirs to do with (within reason of course), and if they don't show up it's no big deal.
Similarly, I won't punish a user for making multiple bookings at the same time & date, so if they're a leader of a big team, they can reserve multiple rooms for their underlings even if they are at the same time & date.
When a slot is booked, the slot is removed from the Room's set of slots, because the Room's set of slots represents *free* slots.
Slots have a fixed amount of time and do not overlap other slots. Slots exist with a half-hour in them, so a slot for 3:00 goes from 3-3:30. If someone wants to book something from 3-5:30 pm, they would book(Me, 304, 6/60, start: 3:00, end: 5:00) and then the book action would remove the 3:00 slot, the 3:30, the 4:00, the 4:30, and the 5:00 slot from the available slots in that room. The booking associated would contain Me, the 3:00, the 3:30, the 4:00, the 4:30, and the 5:00 slot. *The end is an inclusive time.*

### URL Shortener

**concept** URLShortener

**purpose** creates a shorter link that redirects to the longer link provided

**principle** a creator will input a long link into the website. There, they can either autogenerate a suffix or create one of their own. When users click the url that has the suffix attached to it, they will be redirected to the long link.

**state**

a set of LinkAssociators with

&nbsp;&nbsp;&nbsp;&nbsp; a User

&nbsp;&nbsp;&nbsp;&nbsp; a LongLink

&nbsp;&nbsp;&nbsp;&nbsp; a ShortLink

**actions**

**createLinkWithSuffix** (user: User, longLink: String, suffix: String): (shortLink: String)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** for the suffix to not yet exist as the suffix of a ShortLink in a LinkAssociator in the set of LinkAssociators and for the longLink to be a valid URL

&nbsp;&nbsp;&nbsp;&nbsp; **effects** creates a LinkAssociator that contains the user, the longLink, and the shortLink (the domain of the website with the suffix attached to the end). Then, it adds the LinkAssociator to the set of LinkAssociators, and returns the shortLink.

**createLinkAutogenSuffix** (user: User, longLink: String): (shortLink: String)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** for the longLink to be a valid URL

&nbsp;&nbsp;&nbsp;&nbsp; **effects** creates a shortLink (which is the domain name with a suffix that is a random, autogenerated, short string at the end) and checks to see if that shortLink is already associated with a LinkAssociator in its set. If so, it generates another shortLink and tries again until it generates something that isn't already present. Then, creates a LinkAssociator that contains the user, the longLink, and the shortLink. Then, it adds the LinkAssociator to the set of LinkAssociators, and returns the shortLink.

**deleteLink** (user: User, shortLink: String)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** for the shortLink to exist in a LinkAssociator associated with the user in the set of LinkAssociators

&nbsp;&nbsp;&nbsp;&nbsp; **effects** removes the LinkAssociator with the user, longLink, and shortLink in it from the set of LinkAssociators

**redirectLink** (shortLink: String): (longLink: String)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** the shortLink to be associated with a LinkAssociator in the set of LinkAssociators.

&nbsp;&nbsp;&nbsp;&nbsp; **effects** it redirects the user to the longLink found in the LinkAssociator that contains the shortLink.

**additional notes**

A ShortLink is just the domain of the website + suffix. For instance, tinyurl.com/mySuffix is a shortLink.
deleteLink is there so that someone doesn't accidentally camp on a commonly used URL after only using it once.


### Time-Based One-Time Password

**concept** TimelyTemporaryToken

**purpose** to improve security by requiring an additional, time-based, and nonreusable step in gaining access to and authenticating an account, ensuring access to the account can only be possible if you physically possess a selected device and if you know the password

**principle** a user has an account that they link to the generator. Then, the next time they wish to sign into their account, they are prompted to enter the token that appears on their TOTP generator. When the user tries to log in with the token, but the token times out before they can, the authentication fails. When the user enters the token correctly and in a timely manner, authentication succeeds, so this token cannot be used to log into the account again and is thrown out.

**state**
a set of Users with

&nbsp;&nbsp;&nbsp;&nbsp; a set of TokenGenerators

&nbsp;&nbsp;&nbsp;&nbsp; a TimeBeforeDeletion

a TokenGenerator with

&nbsp;&nbsp;&nbsp;&nbsp; an Account

&nbsp;&nbsp;&nbsp;&nbsp; a Token

&nbsp;&nbsp;&nbsp;&nbsp; a Countdown

**actions**

**setTimeBeforeDeletion** (user: User, timeBeforeDelete: TimeBeforeDeletion)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** the timeBeforeDelete to not be negative or zero

&nbsp;&nbsp;&nbsp;&nbsp; **effects** sets the user's timeBeforeDeletion to timeBeforeDelete

**linkAccount** (user: User, account: Account): (tokenGenerator: TokenGenerator)

&nbsp;&nbsp;&nbsp;&nbsp; **effects** creates a new TokenGenerator that contains the account, the user's timeBeforeDeletion as the countdown, and empty token, then adds the tokenGenerator to the user's set of TokenGenerators. Finally, it returns the tokenGenerator.

**unlinkAccount** (user: User, tokenGenerator: TokenGenerator)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** the user to have the tokenGenerator in their set of tokenGenerators

&nbsp;&nbsp;&nbsp;&nbsp; **effects** removes the tokenGenerator from the user's set of tokenGenerators

**generateToken** (user: User, tokenGenerator: TokenGenerator): (token: Token)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** the user to have the tokenGenerator in their set of tokenGenerators

&nbsp;&nbsp;&nbsp;&nbsp; **effects** generates a new, random token and associates it with the tokenGenerator. Then, it resets the tokenGenerator's Countdown to the user's timeBeforeDeletion, returns the token. Then, it begins counting down the tokenGenerator's Countdown. If the user successfully authenticates before the countdown ends or if the countdown ends with no authentication, the token is disassociated from the tokenGenerator, discarded, and can no longer be used.

**authenticate** (user: User, account: Account, token: Token): (success: Boolean)

&nbsp;&nbsp;&nbsp;&nbsp; **requires** the user has a linked TokenGenerator for the account

&nbsp;&nbsp;&nbsp;&nbsp; **effects** verifies that the token matches the value expected from the TokenGenerator associated with this User's account at the current time and that the user's TokenGenerator associated with account still has a valid, positive countdown. Returns true if authentication succeeds, false if not.

**additional notes**

This assumes the user has already logged in on the other side with their username and password in order to set this up, so that we don't have to authenticate this again.

*Benefits*:
This keeps the user safe from data leaks, password guessing, and shoulder-surfing, because you cannot access the account with only the password.

*Risks*:
It doesn't account for if your device is stolen, on which you have your passwords and your TOTP generator. If there is malware on the device that can view the screen, this isn't helpful either, because the moment the person opens the TOTP generator the hacker has access.
