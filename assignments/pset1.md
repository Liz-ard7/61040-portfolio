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
    **requires** registry exists, is active and has a request for this item *so that the sum of counts for this item + countCurrent <= the Request's count Number*.
    **effects** create a new purchase for this purchaser, item, and count. Does NOT decrease the Request's count number.

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

> a username String

> a password String

**actions**

**register** (username: String, password: String): (user: User)

> **requires** the username does not exist

> **effects** creates a new User with the username username and password password, adds it to the set of Users, then returns it

**authenticate** (username: String, password: String): (user: User)

> **requires** requires the username to exist in the set of Users and for said user to have a matching username and password

> **effects** returns the User associated with the username and password

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

> a username String

> a password String

> a token Number

> an emailConfirmed Boolean

**actions**

**register** (username: String, password: String, email: String): (user: User, token: Number)

> **requires** the username does not exist

> **effects** creates a new User with the username username, password password, emailConfirmed boolean False, and token token and adds it to the set of Users. Then, it emails the email provided a special token. Finally, it returns the user created.

**confirm** (username: String, token: Number)

> **requires** the username to exist in the set of Users and for the token to match the token associated with said User.

> **effects** this sets the emailConfirmed Boolean to True.

**authenticate** (username: String, password: String): (user: User)

> **requires** requires the username to exist in the set of Users, for said user to have a matching username and password, and for the emailConfirmed Boolean to be true.

> **effects** returns the User associated with the username and password

---
## Exercise 3

### Specification for PersonalAccessToken

**concept** PersonalAccessToken

**purpose** allow people with a token to access the token holder's resouces with limited permissions

**principle** after a user with an account has created a Personal Access Token, anyone with this Personal Access Token can access the user's resources but with limited permissions (i.e. not "owner" status)

**state**

> a set of Users with

>    a token Token

> a Token with

>    a tokenString String

>    a permission Permission

**actions**

**createToken** (user: User, permission: Permission): (token: Token)

>    **requires** user to be in the set of Users

>    **effects** creates a token by generating a tokenString, then coupling it with the permissions set for the token. Then, it returns the token.

**authenticate** (username: String, tokenString: String): (user: User, token: Token)

>    **requires** the username to be associated with one of the Users in the set of Users and the tokenString to be associated with said User

>    **effects** returns access to the User's resources with token's permissions

### How PersonalAccessToken differs from PasswordAuthentication
A PersonalAccessToken differs from PasswordAuthentication because a PersonalAccessToken is used for people to access a user's account and resources but only with a limited set of permissions or scope, like having 'read-only' permissions and not being allowed to edit any of the files. On the other hand, a password is meant for the user of the account to gain access to *all* of their account, without it being locked behind any extra features. After all, there shouldn't be anyone with higher permissions than the owner of the account, who should have the password (and ideally, be the *only* one to have the password).
It's almost like having a secret painting you're working on, having a ring of keys that you attach to your belt that allows you to access the drawing utensils, paints, box that it is locked in, etc. The ring of keys here is the password, but you can also copy certain keys and hand them to people you trust to do things with them, which is the Personal Access Token. Like, if you want to show your friends, you'll give them the a copy of the key to the box it's kept in, but you won't give them the key to the paints, because they're rather clumsy and you don't trust them *that* much.

### How the Github Documentation could be improved
Put a much higher emphasis on what the token actually does and is meant to be used for than a short blurb. Until I looked very closely at what it said, I was under the impression that these had no difference, after all, they're both ways to access an account. Maybe a section that shows how multiple people or even algorithms can have this token, then access to the account, but not be able to do things the owner forbids.

---
## Exercise 4

### Conference Room Booking

**concept**

**purpose**

**principle**

**state**

**actions**

**additional notes**


### URL Shortener
**concept**

**purpose**

**principle**

**state**

**actions**

**additional notes**


### Time-Based One-Time Password
**concept**

**purpose**

**principle**

**state**

**actions**

**additional notes**
