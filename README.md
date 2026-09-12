# Economia

## A 2D Economics Simulation RPG Built From Scratch in Java

Rodmania is a top-down, Pokemon-style 2D game where the player experiences an economy through everyday decisions. You walk around a town, get a job, take out loans, pay taxes, and watch the national economy react to your decisions and random world events — all while managing your health, satisfaction, and intelligence stats.

The whole game, including the 2D engine underneath it, was written from the ground up in Java with no outside game engine such as LibGDX or Unity.

## Why I Started This Project

I took AP Java in 10th grade and AP Micro/Macroeconomics the same year, and I kept noticing how abstract economics felt on paper — supply and demand curves, interest rate changes, tax policy — without ever feeling their consequences. At the same time, I wanted to build something much bigger than the class assignments and push my Java and object-oriented programming skills further.

So I combined the two. Instead of a slideshow or a written project, I built a life-simulation game where macroeconomic concepts from AP Econ — GDP growth, interest rates, taxation, government spending, and inflation-driving events — are actual game mechanics that the player has to manage to stay healthy, happy, and out of debt.

## How It Works

### 1. Country Simulation

Every in-game "month" (triggered by sleeping in bed), the country class simulates a full macroeconomic cycle:

* Collects taxes from national GDP based on the current tax rate
* Splits government revenue into public spending (70%) and defense spending (20%)
* Grows GDP by the national growth rate
* Draws 3 random news events per month from a pool of ~20 possible events, including tax hikes, spending cuts, interest rate changes, gold rushes, forest fires raising production costs, war, invasion, bankruptcy, and others
* If the country goes to war, a separate pool of war-only events (bombings, battles, surrender, victory) takes over
* Tracks interest rate, tax rate, and spending history in lists and graphs them in-game

### 2. Personal Finance

The player has a purse (cash on hand), a bank balance, and can take out or pay off loans through a Bank NPC.

Each month:

* Income tax is deducted based on the country's tax rate
* House rent is charged, and rent itself scales with the tax rate
* Outstanding debt accrues interest based on the national interest rate
* Bank savings earn interest at the same national rate
* Going into too much debt locks the player out of the bank and lowers Satisfaction and Health

This connects the player's personal finances directly to changes in the country's monetary policy. Raising interest rates makes loans more expensive while making savings more rewarding.

### 3. Jobs, Shops, and Firms

The player can work jobs such as McDonald's Worker or Convenience Store Worker. Jobs require a minimum Intelligence stat and trade Satisfaction for income.

Shops are run by `Firm` objects that sell `Product` objects with independent cost, production cost, and demand. A firm's profit each turn is calculated as demand times (price minus production cost), creating a simplified supply-and-demand model.

### 4. Player Stats and Survival

The player has four main stats: Health, Satisfaction, Intelligence, and Luck.

These stats change based on the player's choices:

* Working raises stress and lowers Satisfaction
* Studying costs money but raises Intelligence
* Debt pressure lowers Satisfaction and eventually Health
* Items such as food and accessories can be bought and consumed to restore stats
* If Health reaches zero, the game ends

### 5. World Interaction

News is delivered through an in-game Newspaper GUI, while monthly outcomes are summarized in an animated Turn Report GUI.

An NPC dialogue system uses branching dialogue trees for tutorials and shop/bank conversations through a Pokemon-style chat box.

### 6. Custom Level Editor

Alongside the game itself, I built a separate Map Editor tool (`mapeditor`) with its own tile palette and save/load system.

This allowed me to design the town's buildings and layouts visually instead of hand-editing the map's text files.

## Technologies

* Java (core language, no external game framework)
* Java AWT / Swing (windowing, custom rendering with `Graphics2D`, GUI panels)
* `javax.sound.sampled` (custom audio playback)
* Object serialization (save/load system)
* Custom-built 2D game engine, including:

  * Fixed-timestep game loop (`GameBase`)
  * AABB hitbox and tile-based collision system
  * Tile map loader and camera/viewport system
  * Keyboard and mouse input managers
  * Screen transition / fade manager
* Custom Map Editor application sharing the same engine

## Source Code

### Core Game

| File                           | Description                                                                                            |
| ------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `gamelogic/Main.java`          | Central game controller, game loop hooks, player stats, save/load, monthly economic tick, and UI state |
| `gamelogic/player/Player.java` | Player movement, stats, inventory, and building interaction                                            |
| `gamelogic/level/Level.java`   | Manages tiles, entities, and the player for a single map/room                                          |

### Economy

| File                                     | Description                                                                                  |
| ---------------------------------------- | -------------------------------------------------------------------------------------------- |
| `gamelogic/world/country.java`           | National economy simulation: GDP, tax, interest rate, spending, random events, and war state |
| `gamelogic/world/events.java`            | Pool of macroeconomic and war event types                                                    |
| `gamelogic/world/Firm.java`              | Business entity that owns Products and earns profit                                          |
| `gamelogic/world/Product.java`           | Individual good with price, production cost, and demand                                      |
| `gamelogic/baseeconomy/moneySupply.java` | Simple money-supply tracker                                                                  |
| `gamelogic/world/BankGUI.java`           | Deposit, withdrawal, and loan UI with interest-rate history graph                            |

### Game Systems and UI

| File                                   | Description                                |
| -------------------------------------- | ------------------------------------------ |
| `gamelogic/world/ShopNPC.java`         | Shop/bank NPC interaction and GUI triggers |
| `gamelogic/world/ShopCategoryGUI.java` | Item browsing and purchase UI              |
| `gamelogic/world/NewspaperGUI.java`    | Displays the month's news events           |
| `gamelogic/world/TurnReportGUI.java`   | Animated end-of-month summary              |
| `gamelogic/world/DialogueManager.java` | Branching dialogue tree definitions        |
| `gamelogic/player/Inventory.java`      | Inventory storage and item logic           |

### Custom Engine

| File                                      | Description                             |
| ----------------------------------------- | --------------------------------------- |
| `gameengine/GameBase.java`                | Abstract game loop (`init/update/draw`) |
| `gameengine/PhysicsObject.java`           | Movement and collision base class       |
| `gameengine/hitbox/RectHitbox.java`       | Axis-aligned bounding box collision     |
| `gameengine/loaders/LeveldataLoader.java` | Parses custom `.txt` map files          |
| `gameengine/graphics/Camera.java`         | Viewport/camera following the player    |

### Map Editor

| File                       | Description                                     |
| -------------------------- | ----------------------------------------------- |
| `mapeditor/MapEditor.java` | Standalone tile-map editor application          |
| `mapeditor/MapSaver.java`  | Saves edited maps to the game's map file format |

## What I Learned

This was by far the largest and most architecturally complex program I had written at that point. Instead of following a tutorial, I had to design my own 2D game engine from scratch — a game loop, tile-based collision, a camera, and asset loaders — before I could even start building the actual economy simulation on top of it.

That forced me to think seriously about class structure and separation of concerns: keeping "engine" code (`gameengine` package) independent from "game" code (`gamelogic` package) so the same engine could also power the separate Map Editor tool.

Translating AP Microeconomics and Macroeconomics concepts into working code was its own challenge. Deciding how tax rate changes should affect rent, how interest rate changes should affect both debt and savings, and how random news events should feed back into growth rate and production costs forced me to actually understand the causal chains behind these concepts well enough to model them, not just recite them for a test.

I also learned a lot about state management in a large program. Keeping player stats, national economic stats, and UI state — which GUI panel is open, whether the game is paused for dialogue, and so on — consistent across many interacting classes was much harder than any single class assignment.

Balancing the simulation so that debt, taxes, and interest rates felt meaningful without making the game unplayable took a lot of iteration. I learned that making a system "technically correct" is very different from making it fun and understandable to someone playing it for the first time.
