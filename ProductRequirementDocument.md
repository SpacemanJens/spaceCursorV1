# Product Requirement Document: Space Stratego

**Version:** 1.0
**Date:** 2023-10-27
**Author:** [Your Name/AI Assistant]

## 1. Overview

Space Stratego is a real-time, multiplayer strategy game inspired by the classic board game Stratego. Two teams (Blue and Green) compete against each other. The game is designed to be a fun, engaging activity, particularly suitable for company team events where teams can coordinate strategy via separate team calls. It leverages p5.js for graphics and p5party for real-time multiplayer functionality.

## 2. Goals

*   Create a functional real-time multiplayer strategy game based on Stratego rules.
*   Provide an engaging and visually appealing experience using p5.js.
*   Facilitate team-based play and strategic coordination.
*   Serve as a fun activity for groups (e.g., company social events).
*   Ensure code is clean, organized, and follows OOP principles.

## 3. Target Audience

*   Employees participating in company social or team-building events.
*   Groups looking for a collaborative online game experience.
*   Fans of strategy games and/or the original Stratego board game.

## 4. Technical Requirements

*   **Frontend:** p5.js
*   **Multiplayer:** p5party library
    *   **Connection Endpoint:** `wss://p5js-spaceman-server-29f6636dfb6c.herokuapp.com`
    *   **Game Namespace:** `jkv-spaceStratego`
*   **Data Synchronization:**
    *   `shared = partyLoadShared("shared", { gameState: “GAME-SETUP” });` (and other shared elements like win counts, canon data, spider data, timer, spawn planets)
    *   `me = partyLoadMyShared({ playerName: "observer" });`
    *   `guests = partyLoadGuestShareds();`
    *   **HOST Responsibility:** Only the client designated as HOST (by p5party) updates the `shared` object.
*   **Programming Style:**
    *   Object-Oriented Programming (OOP).
    *   Clean and organized codebase.
    *   Mandatory Classes: `Spacecraft`, `Canon`, `DecorativeBackgroundStar`, `DecorativeOrangeBackgroundStar`, `StarSystem`, `StarSystemYellowStar`, `StarSystemBlackHole`, `StarSystemPlanet`.
*   **Screen Dimensions:** 2000px width x 1200px height.

## 5. Design & User Interface (UI) Areas

The screen is divided into logical areas:

### 5.1. Game Background Area (Full Screen)

*   **Content:** Dynamic twinkling star background.
*   **Elements:**
    *   20 `DecorativeBackgroundStar` instances:
        *   Random initial positions.
        *   Can glow intermittently.
        *   Can go "super nova": ~2-second animation involving rapid size increase and bright flash. Triggered by specific game events (e.g., clicking small images in setup, warping).
    *   1 `DecorativeOrangeBackgroundStar` instance:
        *   Fixed position (*Question 1: What is the fixed position (x, y coordinates)?*).
        *   Can glow. Triggered by specific game events (e.g., selecting a team).

### 5.2. Game Setup Area (Visible in `GAME-SETUP` state)

*   **Location:** Centered on the screen.
*   **Layout:**
    *   A ring (radius 400px) of 20 small images (120x120px each).
        *   *Question 2: What do these small images represent? Are they related to the Stratego pieces, planets, or just decorative elements?*
        *   **Hover Interaction:** Hovering a small image displays a larger version (800x800px) next to the ring. The large images have animations. Simultaneously, one random `DecorativeBackgroundStar` glows.
        *   **Click Interaction:** Clicking a small image triggers the currently glowing `DecorativeBackgroundStar` to go super nova.
    *   Central Text Block:
        *   Game aim description.
        *   Key controls description (Movement: WASD/TFGH).
    *   Player Name Input Field.
    *   Team Join Buttons (Light Blue & Light Green):
        *   Appear only after a name is entered.
        *   Hidden/replaced with text ("Team Full") if the respective team reaches the player limit (13 players).
        *   **Hover Interaction:** Hovering a team button makes the `DecorativeOrangeBackgroundStar` glow.
        *   **Click Interaction:** Clicking a team button assigns the player to that team. 20 small spacecraft icons zoom towards the `DecorativeOrangeBackgroundStar`. The game view transitions (Character Selection, Game Area, etc., become visible).

### 5.3. Character Selection Area (Visible after joining team)

*   **Location:** Left-hand side of the screen.
*   **Content:** Displays 40 Stratego characters available for the player's team (Blue or Green).
    *   Each entry shows: Character Name (e.g., "Marshal", "Spy", "Flag"), Rank in brackets (e.g., "[10]", "[1]", "[F]").
    *   *Question 3: Can you provide the full list of 40 Stratego characters, their names, and ranks as they should appear?*
*   **Functionality:**
    *   Players see only their own team's list.
    *   Initially (before Flag is chosen by anyone on the team), only the "Flag" is clickable; others are disabled.
    *   Clicking an available character name assigns that character to the player.
    *   The player's name (`me.playerName`) appears next to the chosen character.
    *   Once chosen by a player, a character becomes disabled/unavailable for *all other players on that team*.
    *   After the team's Flag is chosen, all other *available* characters become enabled.

### 5.4. Host Area (Always visible after joining team/in setup)

*   **Location:** Lower-left corner.
*   **Content:** Displays text indicating the host.
    *   For non-hosts: "XXX is Host" (where XXX is `guests[host_id].playerName`).
    *   For the host: "I am Host".

### 5.5. Status Area (Visible after joining team)

*   **Location:** Top-middle part of the screen.
*   **Content:** Displays dynamic informational messages (see Game States for specifics). Also used for battle resolution messages.

### 5.6. Game Area (Visible after joining team)

*   **Location:** Starts at coordinates (600, 50).
*   **Size:** 1200px width x 700px height.
*   **Content:** Shows a cropped, ring-shaped view of the current planet's surface image. The area outside the ring is transparent.
*   **Functionality:** This is the main gameplay area where player-controlled `Spacecraft` move and interact. Players are constrained within the ring boundary.

### 5.7. Minimap Area (Visible after joining team)

*   **Location:** Below the Game Area. (*Question 4: What are the exact coordinates and dimensions for the Minimap area?*)
*   **Content:** Displays a `StarSystem` view.
    *   Central rotating `StarSystemYellowStar` and `StarSystemBlackHole`.
    *   5 orbiting `StarSystemPlanet` instances of different sizes.
    *   Perspective is tilted; planets appear smaller when further away in their orbit.
*   **Functionality:** Acts as a minimap.
    *   Each planet shows dots representing players currently on that planet:
        *   Blue dots for Blue team players.
        *   Green dots for Green team players.
    *   The player's *own* dot is slightly larger and darker.

### 5.8. Statistics Area (Always visible after joining team/in setup)

*   **Location:** Upper-right corner.
*   **Content:**
    *   Team Win Counts: "Blue Wins: [N]", "Green Wins: [M]" (Data from `shared.winCounts`).
    *   Blue Team Player Count: "Blue Team: [X] players"
        *   **Hover Interaction:** Shows a list of player names on the Blue team.
    *   Green Team Player Count: "Green Team: [Y] players"
        *   **Hover Interaction:** Shows a list of player names on the Green team. (*Correction needed: User text says Blue team list shows here too, assuming typo.*)
    *   Observer Count: "Observers: [Z]" (if Z > 0).

### 5.9. Player Controls Area (Visible after joining team)

*   **Location:** Below the Statistics Area.
*   **Content:** Static help text: "Keys WASD moves around on the planet and TFGH moves around on the visible part. Click to shoot (only available for some characters)."

### 5.10. Character Details Area (Visible after joining team)

*   **Location:** Below the Player Controls Area.
*   **Content:**
    *   List/Grid of Stratego character images with their ranks.
    *   Rules Summary Text: "Spy[1] wins vs Marshal[10]. Miner[3] wins vs Bomb[B]. Equal ranks lose both. Flag[F] vs Flag[F] is a draw. Defeating opponent's Flag[F] wins the game." (*Question 5: Confirm ranks and if any other special interactions exist, e.g., Scout movement?*).

## 6. Game Flow & States

The game progresses through distinct states, managed by `shared.gameState`.

### 6.1. `GAME-SETUP` (Initial State)

*   **Visible Areas:** Game Background, Game Setup Area, Statistics Area, Host Area.
*   **Player Actions:**
    *   Enter `playerName`.
    *   Join a team (if not full).
    *   (If team joined) Select the "Flag" character initially.
    *   (After Flag selected) Select another available character.
*   **Host Actions:**
    *   If `me.isHost` is true AND one Flag is selected on *each* team, a "Start Game" button appears for the host.
    *   Clicking "Start Game" transitions `shared.gameState` to `IN-GAME`.
*   **Status Messages (`Status Area`):**
    *   If player joined a team, but team's Flag not yet selected: "A player from your team must select the Flag".
    *   If player's team Flag selected, but player hasn't chosen a character: "Please choose a character".
    *   If player has character, but opponent team Flag not selected: "Waiting for the other team to select the Flag...".
    *   If player has character, both Flags selected, waiting for host: "Waiting for XXX (Host) to start the game".
*   **New Players:** Can join a team during this state (up to the limit). Observers only see Setup, Stats, Host areas.

### 6.2. `IN-GAME`

*   **Visible Areas:** Game Background, Character Selection, Host, Status, Game, Minimap, Statistics, Player Controls, Character Details.
*   **Entry:** Host clicks "Start Game". Host randomly determines and sets initial spawn planets for each team in `shared`.
*   **Gameplay:**
    *   Players spawn on their team's assigned planet.
    *   Players control their `Spacecraft` using WASD (planet global coords) and TFGH (viewport local coords).
    *   Players interact via movement, battle, warp gates, canons, spiders, artefacts.
    *   Battle occurs on contact between opposing team members.
    *   Game ends when one Flag captures the other, or if both Flags battle.
*   **New Players:** Can join a team during this state (up to the limit). They spawn on their team's starting planet. They can select any available character (Flag likely taken).
*   **Players without Characters:** If a player joins late and all characters are taken OR if a player loses their character and none are left, they visually follow their team's Flag carrier (cannot control or act).

### 6.3. `GAME-FINISHED`

*   **Visible Areas:** Same as `IN-GAME`.
*   **Entry:** A Flag is captured, or the two Flags battle.
*   **Display:**
    *   Large text overlay: "Team XXX has won the game!" or "The game ended in a draw".
    *   A random `DecorativeBackgroundStar` goes super nova upon state entry.
    *   Player movement is disabled.
*   **Host Actions:**
    *   If `me.isHost` is true, a "Prepare New Game" button appears.
    *   Clicking resets relevant `shared` data (*except* `shared.winCounts`) and transitions `shared.gameState` back to `GAME-SETUP`.
*   **Player State:** Each client resets their local game state (e.g., selected character, position), retaining only their `playerName`.

## 7. Features (Game Mechanics)

### 7.1. Spacecraft (`Spacecraft` class)

*   **Representation:** Image corresponding to the selected Stratego piece. Encapsulated by a circular boundary for collision/size.
*   **Appearance:**
    *   **Initial:** Opponent team members see a generic, team-colored (blue/green), glowing, semi-transparent "cloaked" spacecraft.
    *   **Revealed:** After its *first* battle, the spacecraft reveals its true character image to opponents, retaining a team-colored glow.
*   **Abilities:**
    *   **Marshal:** Can shoot. Click targets the shot. Max 2 active bullets per Marshal. Bullets removed on hit or leaving Game Area bounds. Hitting an opponent stuns them (unable to move) for 5 seconds.
    *   *Question 6: Do any other pieces have special abilities (e.g., Scout move+attack, Miner defuse Bomb)?*
*   **Movement:** See Movement Mechanics.
*   **Health/Status:** Pieces are removed upon losing a battle.

### 7.2. Movement Mechanics

*   **Controls:**
    *   `WASD`: Controls global position on the planet surface image.
    *   `TFGH`: Controls local position within the visible Game Area viewport (cropping).
*   **Constraints:** The combined position must remain within the planet's boundaries (the ring shape in the Game Area).
*   **Piece Mobility:** All pieces, including Flag and Bombs, can move using WASD/TFGH. (*Confirmation: Stratego Bombs are typically immobile. Is this intended?*)

### 7.3. Battle Mechanics

*   **Trigger:** Two `Spacecraft` from opposing teams touch/overlap.
*   **Process:**
    1.  Both players involved are frozen (cannot move).
    2.  Character cards (Image, Name, Rank) appear next to the battling spacecraft, visible *only* to the two players involved.
    3.  Battle resolution occurs based on rank/special rules (Host likely determines outcome and updates `shared` state if needed, or clients resolve locally based on known info).
    4.  A result message appears in the `Status Area` for each involved player.
*   **Rules:**
    *   Higher Rank wins.
    *   Spy (Rank 1) defeats Marshal (Rank 10).
    *   Miner (Rank 3) defeats Bomb (Rank B).
    *   Bomb (Rank B) defeats any attacking piece except Miner.
    *   Equal Ranks: Both pieces are removed.
    *   Flag vs. Flag: Game ends in a Draw (`GAME-FINISHED` state).
    *   Any Piece vs. Opponent Flag: Attacking team wins (`GAME-FINISHED` state).
*   **Outcomes (for the player):**
    *   **Loss:** Status: "You are a [Your Piece] and lost to a [Opponent Piece]. Please click the mouse button to continue". On click: Your piece is removed from the Game Area and Character Selection list. You can select a new available piece. Battle visuals remain until click.
    *   **Draw (Equal Rank):** Status: "You are a [Your Piece] and had a draw with a [Opponent Piece]. Please click the mouse to continue". On click: Your piece is removed (same as Loss). Battle visuals remain until click.
    *   **Win:** Status: "You are a [Your Piece] and have won against [Opponent Piece]. Please click the mouse button to continue". On click: You regain movement control. Opponent handles their loss. Battle visuals remain until click.
    *   **Win (vs. Flag):** Game ends. Status: "Team [Your Team] has won the game!". `gameState` -> `GAME-FINISHED`.
    *   **Draw (vs. Flag):** Game ends. Status: "The game ended in a draw". `gameState` -> `GAME-FINISHED`.

### 7.4. Warp Gate Mechanics

*   **Location:** Each planet has one "Up" warp gate and one "Down" warp gate at fixed locations.
    *   *Question 7: How are these warp gate locations determined/defined for each planet?*
*   **Functionality:**
    *   Moving into an "Up" gate teleports the player to the "Up" gate of the planet with `index + 1`. Planet `N-1` (highest index) warps to planet `0`.
    *   Moving into a "Down" gate teleports the player to the "Down" gate of the planet with `index - 1`. Planet `0` warps to planet `N-1`.
*   **Visuals/Effects:**
    *   Warp gates have a pulsating or rotating animation.
    *   Short delay upon entering the gate before warping.
    *   At the moment of warping, a *specific* `DecorativeBackgroundStar` associated with that *particular* warp gate goes super nova (bright flash). Each gate needs a unique star assigned.
*   **Cooldown:** After using *any* warp gate, there is a 10-second global cooldown for that player before they can use *any* warp gate again.

### 7.5. Flying Canon Mechanics (`Canon` class)

*   **Location:** Planet with index 0 only.
*   **Quantity:** 3 canons.
*   **Position:** Hover at random locations on Planet 0 (determined by HOST and stored in `shared`).
*   **Behavior:**
    *   Shoot bullets at the nearest player (regardless of team).
    *   Targeting range: 300px.
    *   Hitting a player stuns them (cannot move) for 5 seconds.
    *   Canons do *not* target already stunned players.
*   **Data:** Canon positions, current targets, and bullet data must be stored in `shared` and updated by the HOST.

### 7.6. Passive Object Animations

*   **Purpose:** Enhance visual appeal on planet surfaces.
*   **Examples:** Glittering water, pulsating light beams, etc.
*   **Implementation:** These are purely visual, client-side effects tied to specific planet images/elements.

### 7.7. Active Objects (Spiders)

*   **Location:** Planet with index 4 only.
*   **Behavior:** Spiders walk around the planet surface.
*   **Topology Interaction:** Planet 4 has a hidden grayscale image defining terrain "height".
    *   Spider's visual size dynamically changes based on the grayscale value at its current location (Dark = small, Light = big).
*   **Hazard:**
    *   Touching a spider when it is at its *maximum* size (lightest terrain) causes the player to lose their current piece (removed from game, player returns to character selection if pieces available).
    *   If the *Flag* touches a max-size spider, that team *loses the game* (`gameState` -> `GAME-FINISHED`).
*   **Data:** Spider positions and potentially their current size scale need to be stored in `shared` and updated by the HOST.

### 7.8. Artefacts

*   **Spawning:** Appear randomly on planets. Start appearing after 10 minutes of `IN-GAME` time, increasing in frequency thereafter.
    *   *Question 8: Where exactly do they spawn (random coords)? How are they collected (move over them)? What do they look like?*
*   **Effect:** Collecting an artefact grants a temporary ability:
    *   Ability to shoot (even if not Marshal).
    *   Increased bullet speed.
    *   Increased movement speed.
    *   Ability to jump (teleport short distance?) by clicking on the Game Area. (*Question 9: Clarify "jump" mechanic.*)
    *   Temporary shield (immune to bullets/canon shots).
    *   Temporary cloak (spacecraft becomes nearly invisible).
*   **Duration:** Abilities last for a limited time. (*Question 10: What is the duration for each ability?*)
*   **Data:** Artefact locations and active player buffs might need to be managed in `shared`.

## 8. Resources

*   All required image assets (characters, planets, UI elements, effects) are available.