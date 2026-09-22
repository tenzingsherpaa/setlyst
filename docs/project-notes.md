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
platform** for Princeton student organizations, and a **collaborative set
list tool** for the events those organizations run.

## Target Users

- **Students (general)** — discover events on a campus-wide feed, join or
  request admission to events, request songs, and vote on requests.
- **Club members** — students associated with an organization; see that
  club's private events, are auto-approved for the club's list events, and
  may be allotted guest passes to send to other students.
- **Club officers** — the only users who can create events on behalf of a
  club. They manage club membership, approve attendees, and allot passes.
- **Event organizers** — when an event is declared on behalf of a club, all
  officers of that club become organizers of that event.
- **DJs** — a user assigned by the organizer (per event) who works from the
  ranked song queue and can load it into their own Spotify account.
- **Setlyst admins** — the core dev team, who (as a fallback) manually
  verify club officers.

## Domain Model

### Users and Organizations

- Users can be associated with organizations/clubs as **members** or
  **officers**.
- Only officers can create events on behalf of a club.
- When an event is declared on behalf of a club, **all officers of that club
  are automatically declared organizers** of that event.
- Club membership is managed by that club's officers.
- Officer verification is an open question — see
  [Open Questions](#open-questions).

### Event Visibility

- **Public events** appear on everyone's feed/timeline.
- **Private events** do not appear on the general feed.
  - A private event made on behalf of a club appears on the feeds of that
    club's members.
  - Otherwise, a private event is accessible only via private invite or QR
    code.

### Event Admission

- **Open events** — anyone can join.
- **List events** — organizers must approve attendees.
  - For a list event run by a club, **members of that club are
    automatically approved**.
  - Officers can allot members a number of **passes**. A pass works like a
    digital invitation: a member or officer sends it to another student,
    and the recipient is admitted without needing separate organizer
    approval. Because a pass is sent to a specific student, it is tied to
    that recipient rather than being an anonymous admission slot.

### Song Requests and Voting

- Organizers allot each attendee a **limited or unlimited** number of song
  requests. The cap counts all of an attendee's requests for that event.
- For club events, song requests can be restricted to **club members only**,
  **anyone attending**, or **no one at all**.
- Organizers can likewise limit upvoting to **members only** or
  **attendees**.
- The same song cannot be requested twice for a given event.
- Attendees can change or retract a vote, and may vote on their own request.
- Organizers can set a **cut-off time** after which song requests close.
- The attendee list and song requests persist after the event ends.

## Interfaces

- **Event Feed** — public and accessible private events in chronological
  order.
- **Event Declaring/Organizing Page** — create an event, set visibility,
  admission mode, request/vote permissions, caps, and cut-off time.
- **Event Page** — with distinct Organizer, Attendee, DJ, and Outsider
  views.
- **Club/Student Organization Page** — roster, officers, club events.
- **User Profile Page** - Name, Picture, Club Affiliations.

## Tech Stack

- **Frontend:** React
- **Backend:** Python with Flask
- **Database:** PostgreSQL
- **Deployment:** Render or Heroku
- **Auth:** Princeton CAS SSO
- **Music:** Spotify API (catalog search, plus OAuth for DJ playlist import)

## Core Features (MVP)

**Accounts and organizations**

- Princeton CAS single sign-on for all users.
- Users can be associated with clubs as members or officers; officers manage
  their club's membership.

**Events**

- Officers can create and publish events on behalf of a club; all officers
  of that club become organizers of the event.
- Events can be set public or private, and open or list-admission.
- Chronological event feed showing public events plus private events the
  user can access (club events for club members).
- Private events are reachable by private invite or QR code.
- List events: organizers approve attendees; club members are
  auto-approved; officers allot members guest passes, which members send to
  other students as digital invitations that admit the recipient.

**Song requests and voting**

- Song requests are made by searching the Spotify catalog, so requests carry
  track metadata.
- The same song cannot be requested twice for an event.
- Organizers set a per-attendee request cap (limited or unlimited), and can
  scope who may request and who may upvote (members / attendees / no one).
- Attendees can upvote or downvote requests; one vote per user per request,
  changeable and retractable, and attendees may vote on their own request.
- Requests are displayed to attendees ranked most-to-least upvoted.
- Organizers can set a request cut-off time.
- The attendee list and song requests persist after the event ends.

**DJ**

- Organizers assign a DJ to an event; the DJ sees the ranked queue.
- The DJ connects their own Spotify account and can load the resulting
  playlist into it.

## Stretch Features

- **Set list intelligence** — suggest a song *order* to the DJ based on
  tempo, key, and upvotes, rather than upvotes alone.
- **Smart fill** — suggest popular, on-theme songs that bridge a large BPM
  and key gap between two songs (possible AI/ML component).
- Direct playback control — DJ queues a track straight to a connected
  Spotify device instead of just exporting a playlist.
- Real-time queue updates.
- Organizer analytics (most-requested songs across past events, turnout).
- Notifications when a request gains significant votes or is queued.

## Open Questions

- **How do we verify that a user is genuinely an officer of a club?** We
  plan to contact the Princeton Office of the Dean of Undergraduate Studies
  to see whether they keep officer rosters on file or expose an API. If
  not, officer status will be manually verified by Setlyst admins (the core
  dev team).
