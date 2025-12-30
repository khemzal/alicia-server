# Ranch Chat Commands

This document lists all available chat commands that can be used in the ranch chat by typing `//` followed by the command.

## User Commands

These commands are available to all players.

### General Commands

#### `//about`
Displays information about the server, including version and hosting information.

#### `//help`
Shows a list of available commands and their basic usage.

#### `//create`
Sends you to the character creator on your next login. Your name cannot be changed there.

#### `//online`
Lists all players currently online.

#### `//visit <name>`
Sets a preference to visit another player's ranch when you enter the portal.
- **Parameters:**
  - `<name>` - The name of the player whose ranch you want to visit
- **Example:** `//visit Alice`
- **Note:** The target player's ranch must not be locked.

#### `//emblem <ID>`
Changes your emblem to the specified ID.
- **Parameters:**
  - `<ID>` - The emblem ID number
- **Example:** `//emblem 5`
- **Note:** Requires a client restart to see the change.

---

### Horse Commands

#### `//horse parts <skinId> <faceId> <maneId> <tailId>`
Changes your current mount's visual parts.
- **Parameters:**
  - `<skinId>` - Skin template ID (0 to skip)
  - `<faceId>` - Face template ID (0 to skip)
  - `<maneId>` - Mane template ID (0 to skip)
  - `<tailId>` - Tail template ID (0 to skip)
- **Example:** `//horse parts 1001 2001 3001 4001`
- **Note:** Requires a client restart to see the change.

#### `//horse appearance <scale> <legLength> <legVolume> <bodyLength> <bodyVolume>`
Changes your current mount's body proportions.
- **Parameters:**
  - `<scale>` - Overall size scale (0 to skip)
  - `<legLength>` - Leg length value (0 to skip)
  - `<legVolume>` - Leg volume/thickness (0 to skip)
  - `<bodyLength>` - Body length value (0 to skip)
  - `<bodyVolume>` - Body volume/thickness (0 to skip)
- **Example:** `//horse appearance 100 50 50 50 50`
- **Note:** Requires a client restart to see the change.

#### `//horse potential random`
Randomizes all potential stats for your current mount.
- **Note:** Requires a client restart to see the change.

#### `//horse potential <type> <level> <value>`
Sets a specific potential stat for your current mount.
- **Parameters:**
  - `<type>` - Potential type (1-15, except 12)
  - `<level>` - Potential level
  - `<value>` - Potential value
- **Example:** `//horse potential 1 5 80`
- **Note:** Requires a client restart to see the change.

#### `//horse randomize`
Randomizes both appearance and parts for your current mount.
- **Note:** Requires a client restart to see the change.

---

### Give Commands

These commands add items to your gift storage.

#### `//give horse`
Adds a new random horse to your inventory.
- **Conditions:**
  - You must have fewer than 10 horses
- **Note:** Requires a game restart to see the new horse.

#### `//give item <count> <tid>`
Adds a specified item to your gift storage.
- **Parameters:**
  - `<count>` - Number of items to give (minimum 1)
  - `<tid>` - Template ID of the item
- **Example:** `//give item 5 41001`
- **Note:** Cannot be used for pet eggs (TID 99000-99200).

#### `//give preset <type> [count]`
Adds a preset collection of items to your gift storage.
- **Parameters:**
  - `<type>` - Preset type: `all`, `feed`, `clean`, `play`, `cure`, or `construct`
  - `[count]` - Optional: number of each item (default: 100)
- **Preset Contents:**
  - `feed` - All feeding items (41001-41007)
  - `clean` - Cleaning items (40002, 41008, 41009)
  - `play` - Play items (42001, 42002)
  - `cure` - Cure items (44001-44006)
  - `construct` - Construction items (45001, 46018, 45004)
  - `all` - All of the above
- **Example:** `//give preset feed`

#### `//give carrots <count>`
Adds carrots to your gift storage.
- **Parameters:**
  - `<count>` - Number of carrots to give
- **Example:** `//give carrots 1000`

---

## Admin Commands

These commands are only available to Game Masters and administrators.

### User Management

#### `//users [local]`
Displays an overview of all connected users and their characters.
- **Parameters:**
  - `[local]` - Optional: filter to local users only

#### `//promote <username>`
Promotes a user to Game Master role.
- **Parameters:**
  - `<username>` - The username to promote
- **Example:** `//promote john_doe`
- **Note:** User must be online.

#### `//demote <username>`
Demotes a user from Game Master to regular user role.
- **Parameters:**
  - `<username>` - The username to demote
- **Example:** `//demote john_doe`
- **Note:** User must be online.

#### `//incognito`
Toggles incognito mode for the current Game Master. When enabled, your role appears as "Op" instead of "GameMaster".

---

### Information Commands

#### `//info user <name>`
Displays detailed information about a user.
- **Parameters:**
  - `<name>` - The username
- **Information shown:**
  - Character name and UID
  - Guild membership
  - Current location (ranch/room)
  - Horses owned
  - Pets owned
- **Example:** `//info user john_doe`

#### `//info character <name>`
Displays detailed information about a character.
- **Parameters:**
  - `<name>` - The character name
- **Information shown:** Same as `//info user`
- **Example:** `//info character Alice`

---

### Notice Command

#### `//notice <characterUID> <message>`
Sends a popup notification to one or all players.
- **Parameters:**
  - `<characterUID>` - Character UID to notify, or `0` for all players
  - `<message>` - The message to send
- **Example:** 
  - `//notice 0 Server restart in 5 minutes`
  - `//notice 12345 Please check your inventory`

---

### Infraction Management

#### `//infraction add <username> <type> <duration> [description]`
Adds an infraction (punishment) to a user.
- **Parameters:**
  - `<username>` - The username
  - `<type>` - Punishment type: `none`, `mute`, or `ban`
  - `<duration>` - Duration in format `XXmXXhXXd` (minutes/hours/days) or `forever`
  - `[description]` - Optional: reason for the infraction
- **Examples:**
  - `//infraction add john_doe mute 30m Spam in chat`
  - `//infraction add griefer ban 7d Harassment`
  - `//infraction add cheater ban forever Cheating`
- **Note:** Ban infractions immediately disconnect the user.

#### `//infraction remove <username> <infractionUID>`
Removes an infraction from a user.
- **Parameters:**
  - `<username>` - The username
  - `<infractionUID>` - The UID of the infraction to remove
- **Example:** `//infraction remove john_doe 456`

#### `//infraction list <username>`
Lists all infractions for a user.
- **Parameters:**
  - `<username>` - The username
- **Example:** `//infraction list john_doe`

---

### Moderation Commands

#### `//mod reset user <name>`
Resets a user's character, forcing them to create a new one.
- **Parameters:**
  - `<name>` - The username
- **Example:** `//mod reset user john_doe`
- **Warning:** This disconnects the user from all game systems.

#### `//mod rename horse <uid> <name>`
Renames a horse.
- **Parameters:**
  - `<uid>` - The horse UID
  - `<name>` - The new name (can contain spaces)
- **Example:** `//mod rename horse 12345 Thunder Storm`

#### `//mod rename pet <uid> <name>`
Renames a pet.
- **Parameters:**
  - `<uid>` - The pet UID
  - `<name>` - The new name (can contain spaces)
- **Example:** `//mod rename pet 67890 Fluffy`

#### `//mod rename guild <uid> <name>`
Renames a guild.
- **Parameters:**
  - `<uid>` - The guild UID
  - `<name>` - The new name (can contain spaces)
- **Example:** `//mod rename guild 111 The Knights`

#### `//mod rename room <uid> <name>`
Renames a racing room.
- **Parameters:**
  - `<uid>` - The room UID
  - `<name>` - The new name (can contain spaces)
- **Example:** `//mod rename room 222 Speed Track`

---

## Notes

- Most horse-related commands require a client restart to see changes
- Gift storage items can be claimed from your storage/inventory
- Admin commands are logged in the server logs
- Some commands have character or inventory limits (e.g., max 10 horses)

## Implementation

All commands are implemented in `src/server/system/ChatSystem.cpp`:
- User commands: `RegisterUserCommands()` function
- Admin commands: `RegisterAdminCommands()` function

Commands are processed when a message starts with `//` in ranch chat.
