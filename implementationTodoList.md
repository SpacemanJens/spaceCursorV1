# Space Stratego - Implementation Todo List

## Phase 1: Project Setup & Core Structures

*   [ ] **1.1. Initialize Project:**
    *   [ ] Set up a new p5.js project.
    *   [ ] Set canvas size to `2000x1200`.
*   [ ] **1.2. Implement p5party Connection:**
    *   [ ] Add p5party library to the project.
    *   [ ] Implement `partyConnect("wss://p5js-spaceman-server-29f6636dfb6c.herokuapp.com", "jkv-spaceStratego");`
    *   [ ] Initialize `shared = partyLoadShared("shared", { gameState: "GAME-SETUP" /* Add other shared defaults: win counts, canon data stub, spider data stub, timer stub, spawn planets stub */ });`
    *   [ ] Initialize `me = partyLoadMyShared({ playerName: "observer" /* Add other player-specific defaults: team: null, character: null, position: null, etc. */ });`
    *   [ ] Initialize `guests = partyLoadGuestShareds();`
*   [ ] **1.3. Implement Core Classes (Structure Only):**
    *   [ ] Create `Spacecraft.js` with basic constructor.
    *   [ ] Create `Canon.js` with basic constructor.
    *   [ ] Create `DecorativeBackgroundStar.js` with basic constructor.
    *   [ ] Create `DecorativeOrangeBackgroundStar.js` with basic constructor.
    *   [ ] Create `StarSystem.js` with basic constructor.
    *   [ ] Create `StarSystemYellowStar.js` with basic constructor.
    *   [ ] Create `StarSystemBlackHole.js` with basic constructor.
    *   [ ] Create `StarSystemPlanet.js` with basic constructor.
*   [ ] **1.4. Implement HOST Logic Foundation:**
    *   [ ] Create a mechanism (e.g., a helper function or check within `draw()`/`update()`) to determine if `partyIsHost()`.
    *   [ ] Establish the pattern that only the HOST modifies the `shared` object for game state logic.

## Phase 2: Visual Foundations & Background

*   [ ] **2.1. Implement Game Background Area:**
    *   [ ] Draw a basic black background.
    *   [ ] Implement twinkling star effect across the entire background.
*   [ ] **2.2. Implement Decorative Background Stars:**
    *   [ ] Instantiate 20 `DecorativeBackgroundStar` objects with random positions.
    *   [ ] Implement `draw()` method for stars (basic static appearance).
    *   [ ] Implement `glow()` method (visual change).
    *   [ ] Implement `superNova()` method (animation: rapid size increase, bright flash, reset state). ~2 seconds duration.
*   [ ] **2.3. Implement Decorative Orange Background Star:**
    *   [ ] Instantiate 1 `DecorativeOrangeBackgroundStar` at its fixed location.
    *   [ ] Implement `draw()` method.
    *   [ ] Implement `glow()` method.

## Phase 3: Game State - GAME-SETUP

*   [ ] **3.1. Implement Basic State Handling:**
    *   [ ] In the main draw loop, use `if (shared.gameState === "GAME-SETUP") { ... }` to control rendering.
*   [ ] **3.2. Implement Game Setup Area UI:**
    *   [ ] Draw the ring of 20 small images (120x120) at the center (Radius 400). (Use placeholders if final images aren't integrated yet).
    *   [ ] Draw the central text block (Game Aim, Controls).
    *   [ ] Implement player name input field.
    *   [ ] Implement "Join Blue Team" and "Join Green Team" buttons (initially hidden).
*   [ ] **3.3. Implement Game Setup Area Interactions:**
    *   [ ] **Name Input:** Update `me.playerName` when text is entered. Show Join buttons only if `me.playerName` is not empty / "observer".
    *   [ ] **Small Image Hover:**
        *   Detect hover over small images.
        *   Display the corresponding large (800x800) animated image next to the ring.
        *   Trigger `glow()` on one random `DecorativeBackgroundStar`. Store which star is glowing.
    *   [ ] **Small Image Click:**
        *   Detect click on small images.
        *   Trigger `superNova()` on the currently glowing `DecorativeBackgroundStar`.
    *   [ ] **Team Button Hover:**
        *   Detect hover over team buttons.
        *   Trigger `glow()` on the `DecorativeOrangeBackgroundStar`. (Ensure it stops glowing when not hovered).
    *   [ ] **Team Button Click (Joining Team):**
        *   Detect click on team buttons.
        *   Check team player count (`guests` array filtered by team) against the limit (13).
        *   If space available: Update `me.team` (e.g., "blue" or "green").
        *   Trigger animation: 20 small spacecraft icons zoom to the `DecorativeOrangeBackgroundStar`.
        *   Change player's view: Hide Game Setup Area central elements, reveal Character Selection, Host, Status, Game, Minimap, Statistics, Player Controls, Character Details areas. (This involves changing what's drawn based on `me.team` not being null).
        *   If team full: Disable/hide the button and show "Team Full" text.
*   [ ] **3.4. Implement Statistics Area (Setup View):**
    *   [ ] Draw Win Counters (initially 0). Read from `shared.winCounts`.
    *   [ ] Calculate and draw Blue/Green team counts based on `guests` data.
    *   [ ] Implement hover interaction for team counts to show player lists.
    *   [ ] Calculate and draw Observer count.
*   [ ] **3.5. Implement Host Area (Setup View):**
    *   [ ] Display "XXX is Host" or "I am Host" based on `partyIsHost()`.
*   [ ] **3.6. Implement Character Selection Area (Initial State):**
    *   [ ] Draw the area background/frame.
    *   [ ] Display the list of 40 characters (Name [Rank]) for the player's team (`me.team`).
    *   [ ] Initially, only the "Flag" character is clickable; others are visually disabled.
    *   [ ] Show player names next to characters *already taken* by others on the team (read from `guests`).
*   [ ] **3.7. Implement Character Selection Logic:**
    *   [ ] **Flag Selection:** Allow click on "Flag" if available. Update `me.character` = "Flag". Disable "Flag" in the list. Display player's name next to "Flag". Enable other *available* characters.
    *   [ ] **Other Character Selection:** Allow click on other enabled+available characters. Update `me.character`. Disable the chosen character. Display player's name next to it.
    *   [ ] Update Character Selection display based on `guests` data to show taken characters and disable them.
*   [ ] **3.8. Implement Status Area Messages (Setup):**
    *   [ ] Display messages based on conditions: "A player from your team must select the Flag", "Please choose a character", "Waiting for the other team...", "Waiting for XXX (Host) to start...". Requires checking `me.character`, `shared` flag status for both teams.
*   [ ] **3.9. Implement Host Start Game Logic:**
    *   [ ] **Button:** If `partyIsHost()` AND a Flag is selected on *both* teams (check `guests`), display "Start Game" button for the Host.
    *   [ ] **Action:** On Host click:
        *   HOST: Randomly choose spawn planets for Blue and Green teams. Store in `shared.spawnPlanets`.
        *   HOST: Set `shared.gameState = "IN-GAME"`.
        *   HOST: Potentially initialize other `IN-GAME` shared data (timer, canon positions, spider positions).

## Phase 4: Game State - IN-GAME

*   [ ] **4.1. Implement State Transition:**
    *   [ ] In the main draw loop, use `if (shared.gameState === "IN-GAME") { ... }` to control rendering and updates.
    *   [ ] Ensure all required UI areas are displayed: Character Selection, Host, Status, Game, Minimap, Statistics, Player Controls, Character Details.
*   [ ] **4.2. Implement Player Spawning:**
    *   [ ] When state transitions to `IN-GAME` or a player joins mid-game:
        *   Read team spawn planet from `shared.spawnPlanets`.
        *   Instantiate the player's `Spacecraft` object based on `me.character`.
        *   Set initial position on the spawn planet. (*Need logic for initial placement within the planet's bounds*).
*   [ ] **4.3. Implement Game Area:**
    *   [ ] Load and display the cropped ring image for the player's current planet.
    *   [ ] Draw player's `Spacecraft` at its current position within the Game Area bounds.
    *   [ ] Draw other players' `Spacecraft` that are on the same planet and within the visible crop.
*   [ ] **4.4. Implement Spacecraft Appearance:**
    *   [ ] Implement logic to track if a `Spacecraft` has been in battle (`me.revealed = false` initially).
    *   [ ] Draw opponent spacecraft: If `opponent.revealed` is false, draw cloaked version. If true, draw actual character image with team glow.
    *   [ ] Draw own spacecraft: Always show true image with team glow.
*   [ ] **4.5. Implement Movement Mechanics:**
    *   [ ] Capture `WASD` key presses for global planet movement. Update player's position data (`me.position` or similar).
    *   [ ] Capture `TFGH` key presses for local viewport movement. Update view offset/crop parameters.
    *   [ ] Implement boundary checks to keep the player within the planet's ring shape.
    *   [ ] Ensure Flags and Bombs are movable.
*   [ ] **4.6. Implement Minimap Area:**
    *   [ ] Instantiate `StarSystem` object.
    *   [ ] Implement `StarSystemYellowStar` and `StarSystemBlackHole` classes with rotation.
    *   [ ] Instantiate 5 `StarSystemPlanet` objects with different sizes and orbital paths (implement perspective scaling).
    *   [ ] Implement drawing dots on planets: Iterate through `guests` and `me`, draw blue/green dots on the correct planet based on player location. Make `me.dot` slightly larger/darker.
*   [ ] **4.7. Implement Passive Object Animations:**
    *   [ ] Add animations (glittering, pulsating) to elements on planet surface images (client-side effect).
*   [ ] **4.8. Implement Battle Mechanics:**
    *   [ ] **Collision Detection:** Detect overlap between own `Spacecraft` and opponent `Spacecraft`.
    *   [ ] **Battle State Trigger:** When collision detected, set a battle state flag for both involved players (e.g., `me.inBattle = true`, potentially update `shared` if needed for synchronization). Freeze movement for battling players.
    *   [ ] **Character Card Display:** If `me.inBattle`, draw opponent's character card (image, name, rank) next to their spacecraft. (Only visible locally to the two players in battle). *Requires knowing the opponent's character.*
    *   [ ] **Battle Resolution:**
        *   Determine winner/loser/draw based on ranks and special rules (Spy/Marshal, Miner/Bomb, Equal Ranks, Flag/Flag, Piece/Flag). This logic likely runs on both clients or is determined by Host and synced via `shared`.
        *   Update opponent's `revealed = true` status (locally or via `shared`). Update own `me.revealed = true`.
    *   [ ] **Outcome Display & Action:**
        *   Display appropriate message ("You lost...", "You drew...", "You won...") in Status Area.
        *   Wait for mouse click.
        *   **On Loss/Draw:** Remove player's `Spacecraft` object. Clear `me.character`. Update Character Selection list (make the lost character available again *for others* if it was unique?). Allow player to choose a new character if available. Reset `me.inBattle`.
        *   **On Win:** Re-enable movement. Reset `me.inBattle`. (Opponent handles their loss).
        *   **On Flag Battle (Win/Draw):** Trigger game end immediately. HOST sets `shared.gameState = "GAME-FINISHED"`. Update `shared.winCounts` if applicable (Host only).
*   [ ] **4.9. Implement Warp Gate Mechanics:**
    *   [ ] Define fixed warp gate locations on each planet image/data.
    *   [ ] Draw warp gates with pulsating/rotating animation.
    *   [ ] Detect player collision/entry into a warp gate.
    *   [ ] Implement 10-second cooldown timer (`me.warpCooldown`). Start timer after warping. Prevent warping if timer active.
    *   [ ] **Warp Action:**
        *   Short delay.
        *   Trigger `superNova()` on the specific `DecorativeBackgroundStar` associated with *that* gate.
        *   Calculate destination planet index based on Up/Down gate and current planet index.
        *   Update player's planet location (`me.currentPlanetIndex`).
        *   Update player's position to the corresponding gate location on the new planet.
        *   Reset cooldown timer.
*   [ ] **4.10. Implement Flying Canon Mechanics (Planet 0):**
    *   [ ] **HOST ONLY:**
        *   On `IN-GAME` start, randomly determine 3 initial hover positions for canons on Planet 0. Store in `shared.canonData`.
        *   Periodically update canon logic: Find nearest player within 300px range (excluding stunned players).
        *   If target found, create bullet data (position, target, velocity) and add to `shared.canonData.bullets`.
        *   Update bullet positions in `shared.canonData.bullets`. Check for hits or out-of-bounds. Remove bullet data on hit/miss.
        *   If a bullet hits a player, update player's status in `shared` (e.g., `shared.playerStatus[playerId] = { stunned: true, stunTimer: 5.0 }`).
        *   Decrement stun timers in `shared`.
    *   [ ] **ALL CLIENTS:**
        *   Read `shared.canonData`.
        *   Draw canons at their current positions if player is on Planet 0.
        *   Draw bullets based on `shared.canonData.bullets`.
        *   Check `shared.playerStatus` or local state (`me.isStunned`) to disable movement if hit. Implement 5-second stun timer locally based on shared state change.
*   [ ] **4.11. Implement Active Objects - Spiders (Planet 4):**
    *   [ ] **HOST ONLY:**
        *   Load hidden grayscale topology image for Planet 4.
        *   Initialize spider positions and store in `shared.spiderData`.
        *   Periodically update spider positions based on some movement logic (e.g., random walk). Store new positions in `shared.spiderData`.
        *   Determine spider size based on grayscale value at its current position. Store scale in `shared.spiderData`.
        *   Check for collisions between players and max-size spiders.
        *   If collision with max-size spider: Update player status in `shared` (lost piece).
        *   If FLAG collides with max-size spider: Set `shared.gameState = "GAME-FINISHED"`, update `shared.winner` (opposing team).
    *   [ ] **ALL CLIENTS:**
        *   Read `shared.spiderData`.
        *   Draw spiders at their positions with size scaled according to `shared.spiderData.scale` if player is on Planet 4.
        *   Handle own piece removal if `shared` indicates loss due to spider collision (similar to battle loss).
*   [ ] **4.12. Implement Artefact Mechanics:**
    *   [ ] **HOST ONLY:**
        *   Implement game timer check (start spawning > 10 mins).
        *   Implement logic for spawning artefacts at random locations on planets with increasing frequency. Store artefact data (type, location, id) in `shared.artefacts`.
        *   Detect player collision with artefacts.
        *   If collected: Remove artefact from `shared.artefacts`. Grant temporary ability to player by updating `shared.playerStatus[playerId]` (e.g., `{ ability: 'shoot', duration: 15.0 }`).
        *   Decrement ability duration timers in `shared`. Remove ability when timer expires.
    *   [ ] **ALL CLIENTS:**
        *   Read `shared.artefacts`. Draw visible artefacts on the correct planets.
        *   Read `shared.playerStatus` for own temporary abilities (`me.activeAbility`).
        *   Implement ability effects locally:
            *   **Shoot:** Allow click-to-shoot even if not Marshal.
            *   **Faster Bullets:** Modify bullet speed calculation.
            *   **Faster Movement:** Modify movement speed calculation.
            *   **Jump:** Implement click-on-game-area-to-teleport logic.
            *   **Shield:** Ignore bullet/canon hits.
            *   **Cloak:** Change own spacecraft appearance to nearly invisible.
*   [ ] **4.13. Implement Player Controls / Character Details Area Display:**
    *   [ ] Ensure these static areas are drawn correctly during `IN-GAME`.
*   [ ] **4.14. Handle Players without Characters:**
    *   [ ] If `me.character` is null (joined late, lost piece and none available): Implement camera logic to follow the team's Flag carrier's view. Disable all actions for this player.

## Phase 5: Game State - GAME-FINISHED

*   [ ] **5.1. Implement State Transition:**
    *   [ ] In the main draw loop, use `if (shared.gameState === "GAME-FINISHED") { ... }` to control rendering.
    *   [ ] Ensure player movement and actions are disabled.
*   [ ] **5.2. Implement Game Finished Display:**
    *   [ ] Trigger a random `DecorativeBackgroundStar.superNova()` once upon entering the state.
    *   [ ] Display large "Team XXX Won!" or "Game is a Draw!" text overlay based on `shared.winner` or draw condition.
    *   [ ] Keep displaying the final game board state (Characters, Minimap etc.).
*   [ ] **5.3. Implement Host "Prepare New Game" Logic:**
    *   [ ] **Button:** If `partyIsHost()`, display "Prepare New Game" button.
    *   [ ] **Action:** On Host click:
        *   HOST: Reset relevant `shared` data (e.g., `shared.spawnPlanets = null`, `shared.canonData = {}`, `shared.spiderData = {}`, `shared.artefacts = []`, `shared.playerStatus = {}`, but *keep* `shared.winCounts`).
        *   HOST: Set `shared.gameState = "GAME-SETUP"`.
*   [ ] **5.4. Implement Client Reset:**
    *   [ ] When `shared.gameState` changes *from* `GAME-FINISHED` *to* `GAME-SETUP`:
        *   Each client resets their local state: `me.character = null`, `me.team = null` (or keep team?), `me.position = null`, `me.revealed = false`, `me.warpCooldown = 0`, etc. (Keep `me.playerName`).

## Phase 6: Refinement & Testing

*   [ ] **6.1. Code Cleanup:** Review code for organization, clarity, and adherence to OOP principles.
*   [ ] **6.2. Asset Integration:** Replace placeholder graphics with final images.
*   [ ] **6.3. Animation Polish:** Ensure all animations (supernova, zoom, warp pulse, passive objects) are smooth.
*   [ ] **6.4. Multiplayer Testing:** Test thoroughly with multiple clients, focusing on:
    *   State synchronization.
    *   Host handover (if p5party supports it gracefully, otherwise test host leaving).
    *   Latency effects.
    *   Correctness of shared data updates (Host only).
    *   Race conditions.
*   [ ] **6.5. Gameplay Balancing:** Playtest to check fairness, pacing, artefact usefulness, canon/spider difficulty.
*   [ ] **6.6. Bug Fixing:** Address any issues found during testing.