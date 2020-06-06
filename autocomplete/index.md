Autocomplete and Typeahead are two common names for a very specific form of what I'd call "contextual search implicitly triggered by interactions". The general problem definition can be stated as something like:

Given a context and user
When that user enters in some text
Then the UI should attempt to predict what that user would type next

It's the cyborg equivalent of someone completing your sentences / thoughts for you.

Some common use-cases for this include:

* Phone text editors suggesting the rest of the word you're typing
* Autocomplete lists in search UIs
* Chat software's @user and #hashtag drop-downs
* Google's search input box
* Gmail attempting to auto-complete your sentences (or in some cases your entire email)

While those are loosely similar interaction patterns, they have very different technical considerations. Some considerations around these are discussed below.

# Chat Software @user and #hashtag completes

These days, every chat program out there allows you to directly ping specific individuals (or bots) by entering in @ followed by some characters. Depending on the type of software, and the scope this can be implemented very differently.

The first consideration is understanding the architecture of the chat software itself. The Software Engineering Daily podcast has [an episode on Facebook Messenger](https://softwareengineeringdaily.com/2020/03/31/facebook-messenger-engineering-with-mohsen-agsen/), discussing some of the ways a modern chat client is implemented. The main summary is modern chat software architecture is not a simple client-server interaction (which would've been my naive assumption previously), but is much closer to an App interacting with a Local DB than a simple client-server architecture.

![client server chat client](chat-client-server-architecture-v1.png)

In particular, in these chat apps, for performance and off-line usage reasons, most of the interactions are App <-> Local DB (possibly SQLite) and then LocalDB <--synchronizer--> Remote Server.

![chat client with a db](chat-db-architecture-v1.png)

Those two different architectures could handle autocomplete very differently. If you assumed the client has all the user information already, then autocomplete is a client-side feature and the server side part is related to the data synchronization.

However, that may not be a valid assumption, and not all clients would be _that_ smart/rich. Additionally, there may be use-cases where managing the entire dataset constantly is computationally/memory/generally expensive. For example, some chat software hides full user lists in large rooms or organizations.
