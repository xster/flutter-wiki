# Chatting on Discord

The Flutter team uses a Discord server, which you are [invited to join](https://discord.gg/BS8KZyg).

We recommend you use the same display name on Discord and GitHub.

## Existing channels

We have different channels for different purposes:

| Channel | Description | Participants |
| - | - | - |
| #welcome | Welcome message. | Only admins can post.<br>Anyone can read.
| #announcements | Flutter announcements (e.g. breaking changes). | Only team members can post (but not frequently).<br>Anyone can read.
| #general | Chat about anything related to Flutter. | Anyone.
| #help | People asking for our help. | Anyone.
| #dart | Questions about the Dart language. | Anyone.
| #tree-status | Announcements about whether the trees are open or closed. | Anyone, but discussion should happen elsewhere.
|#hackers | Chat about anything related to Flutter. | Flutter contributors.<br>Anyone can watch.
|#hackers-* | Chat specifically about foo, where foo is something to do with Flutter development, for example "engine", "framework", "add-to-app", etc. | Flutter contributors.<br>Anyone can watch.
|#hackers-new | New people to the team and people who want to help them. | Flutter contributors.<br>Anyone can watch.
|#hackers-triage | For use while triaging bugs. | Flutter contributors.<br>Anyone can watch.
|#hidden-chat | Chat about anything related to Flutter. | Flutter contributors only.<br>Not public.
|#server-support | Forum for asking for your role to be changed, for code of conduct violations to be raised, and for other administrative issues. | Anyone.
|general (voice) | Talk (audio) about anything related to Flutter. | Anyone.

Most channels have a rate limit applied which reduces the frequency with which people who are not yet "team members" (contributors) can post.

The #hidden-chat channel is the only channel that is not publicly-readable; you have to be a member of the "team" role to see it.

## New channels

If you need a new channel, raise the topic in #server-support. Channels are cheap.

If you find your team's channel is hard to follow due to having too many topics discussed at once, ask for a new channel.

If you would like a channel for subcommunities, e.g. #women or #china, that should be fine. (Subcommunities that are already overrepresented in the main channels, e.g. #english-speakers, are less likely to be good candidates for dedicated channels.)

Each channel describes its topic in the channel description. Please read the channel description before posting in a channel, to make sure you're using the appropriate one.

If you can't find an appropriate channel, use #hackers or ask for a new channel to be created. Again, channels are cheap.

We currently lump all the text channels into a single category. Discord supports different categories and as we evolve the server we may start organizing differently. For now, we are keeping things simple.

## Policies

Direct messages are disabled on the server by default. If you wish to direct-message someone, you will need to become mutual friends.

See the [[contributor access]] wiki page for details on becoming a member of the "team" role.


# Design documents

If you want to write a design doc for people to review, we recommend using Google Docs.
We have a template you can use, at [flutter.dev/go/template](https://flutter.dev/go/template). It describes the process for minting a `flutter.dev/go/foo` shortlink for your design doc.
We recommend you use that template so that people can immediately recognize that this is a Flutter design document and that it is shared publicly.

When you implement a design, document it in the source code in detail. The API documentation is the usual place where we document our designs. It's perfectly reasonable for API docs to be multiple pages long with subheadings (e.g. see the docs for [RenderBox](https://master-api.flutter.dev/flutter/rendering/RenderBox-class.html)!). Do not assume that anyone will ever read your design doc after the discussion has finished. Similarly, do not assume that anyone will look at closed GitHub issues or PR discussions.
