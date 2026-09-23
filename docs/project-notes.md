# Project Notes

## Project Name

Setlyst

## Problem Statement

Setlyst addresses three connected gaps in Princeton student social life:

1. **Event discovery.** There is currently no central place to see all the
   social events going on across the university. Events are scattered across
   group chats, flyers, listservs, and social media.
2. **List management.** Events that require an invitation or put people on a
   "list" rely on crude, ad-hoc list management — spreadsheets, text
   threads, a person at the door with a phone. There is no shared system for
   approving attendees or handing out guest passes.
3. **Music curation.** Attendees have no structured way to request songs, and
   the person running music (an assigned DJ) has no shared, ranked view of
   what the crowd actually wants to hear.

Setlyst is therefore dual-purpose: an **event publication and admission
platform** for Princeton students and student organizations, and a
**collaborative set list tool** for the events they run.

## Target Users

All users are Princeton students signing in through CAS. The roles below
build on each other: every club officer is also a club member, and a
single student can hold different roles at different events.

- **Students (general)** — discover events on a campus-wide feed, join or
  request admission to events, request songs, and vote on requests. Any
  student can also create their own individual events.
- **Club members** — students associated with an organization. They are
  auto-approved for the club's list events. As stretch features, they will
  also see that club's private events and may be allotted guest passes to
  send to other students.
- **Club officers** — the only users who can create events on behalf of a
  club. They manage club membership and approve attendees, and (stretch)
  allot guest passes.
- **Event organizers** — the users who run a specific event: they set its
  admission mode, song-request cap, and request cut-off time, approve
  attendees, and assign the DJ. As stretch features, they will also set
  the event's visibility and who may request songs or vote. When an event is declared on behalf of a club, all
  officers of that club become its organizers.
- **DJs** — users assigned by an organizer to a specific event. They work
  from the ranked song queue and can load it into their own Spotify
  account.

### Test Users

- **Event Organizer / Club Officer** — Mark Brusasco
- **Student / Event Attendee** — Isaac Longo
- **DJ** — Rohan Pahwa

## Domain Model

### Users and Organizations

- Users can be associated with organizations/clubs as **members** or
  **officers**.
- Any user can create an individual event; the creator is its organizer.
- Only officers can create events on behalf of a club.
- When an event is declared on behalf of a club, **all officers of that club
  are automatically declared organizers** of that event.
- Club membership is managed by that club's officers.
- Officer verification is an open question — see
  [Open Questions](#open-questions).

### Event Visibility

- **Public events** appear on everyone's feed/timeline. The MVP supports
  public events only.
- **Private events** (stretch) do not appear on the general feed.
  - A private event made on behalf of a club appears on the feeds of that
    club's members.
  - Otherwise, a private event is accessible only via private invite or QR
    code.

### Event Admission

- **Open events** — anyone can join.
- **List events** — organizers must approve attendees. In the MVP, the
  list is managed only by the event's organizers.
  - For a list event run by a club, **members of that club are
    automatically approved**.
  - (Stretch) Officers can allot members a number of **passes**. A pass
    works like a digital invitation: a member or officer sends it to another student,
    and the recipient is admitted without needing separate organizer
    approval. Because a pass is sent to a specific student, it is tied to
    that recipient rather than being an anonymous admission slot.

### Song Requests and Voting

- Organizers allot each attendee a **limited or unlimited** number of song
  requests. The cap counts all of an attendee's requests for that event.
- By default, any attendee can request songs.
- (Stretch) For club events, song requests can be restricted to **club
  members only**, **anyone attending**, or **no one at all**.
- By default, any attendee can upvote or downvote a request. Each request
  is ranked by its **net score** (upvotes minus downvotes), as on Reddit.
- (Stretch) Organizers can restrict voting to **club members only**,
  **attendees**, or **no one**.
- The same song cannot be requested twice for a given event.
- Attendees can change or retract a vote, and may vote on their own request.
- Organizers can set a **cut-off time** after which song requests close.
- The attendee list and song requests persist after the event ends.

## Interfaces

- **Event Feed** — public events in chronological order (plus accessible
  private events, once private events are added).
- **Event Declaring/Organizing Page** — create an event, set admission
  mode, request caps, and cut-off time (plus visibility and request/vote
  permissions, once those stretch features are added).
- **Event Page** — with distinct Organizer, Attendee, DJ, and Non-attendee
  views. The Non-attendee view is for students who have not joined or been
  admitted yet; it lets them join an open event or request to join a list
  event.
- **Club/Student Organization Page** — roster, officers, club events.
- **User Profile Page** — name, picture, club affiliations.

## Tech Stack

- **Frontend:** React
- **Backend:** Python with Flask
- **Database:** PostgreSQL
- **Deployment:** Render
- **Auth:** Princeton CAS SSO
- **Music:** Spotify API (catalog search, plus OAuth for DJ playlist import)

## Core Features (MVP)

**Accounts and organizations**

- Princeton CAS single sign-on for all users.
- Users can be associated with clubs as members or officers; officers manage
  their club's membership.
- Premade core student organizations to start.

**Events**

- Any user can create an individual event, and officers can create events
  on behalf of a club; all officers of that club become organizers of the
  event.
- All MVP events are public, and each is either open or list-admission.
- Chronological event feed showing all public events.
- List events: organizers approve attendees; club members are
  auto-approved for their club's events; others can request to join.

**Song requests and voting**

- Song requests are made by searching the Spotify catalog, so requests carry
  track metadata.
- Any attendee can upvote or downvote requests; one vote per user per
  request, changeable and retractable, and attendees may vote on their own
  request.
- Requests are displayed to attendees ranked by net score (upvotes minus
  downvotes), highest first.
- The same song cannot be requested twice for an event.
- Any attendee can request songs; organizers set a per-attendee request cap
  (limited or unlimited).
- Organizers can set a request cut-off time.
- The attendee list and song requests persist after the event ends.

**DJ**

- Organizers assign a DJ to an event; the DJ sees the ranked queue.
- The DJ connects their own Spotify account and can load the resulting
  playlist into it.

## Stretch Features

- **Private events** — events hidden from the general feed. Private club
  events appear on club members' feeds; other private events are reachable
  only by private invite or QR code.
- **Guest passes** — officers allot members guest passes, which members
  send to other students as digital invitations that admit the recipient.
- **Request and vote permissions** — organizers can restrict who may
  request songs and who may vote to club members only, attendees, or no
  one (MVP default: any attendee can do both).
- **Set list intelligence** — suggest a song *order* to the DJ based on
  tempo, key, and upvotes, rather than upvotes alone.
- **Smart fill** — suggest popular, on-theme songs that bridge a large BPM
  and key gap between two songs (possible AI/ML component).
- **Direct playback control** — the DJ plays music directly from the app,
  working through the ranked queue, instead of just exporting a playlist
  to Spotify.
- **Organizer analytics** — tracks most-requested songs across past events,
  and turnout.

## Risks

- **Fraud** — users posing as club officers and posting false events.
- **Privacy** — users may want to contribute to an event playlist but not
  want to appear as an event attendee.

## Open Questions

- **How do we verify that a user is genuinely an officer of a club?** We
  plan to contact the Princeton Office of the Dean of Undergraduate
  Students (ODUS) to see whether they keep officer rosters on file or
  expose an API. If not, officer status will be manually verified by Setlyst admins (the core
  dev team).


