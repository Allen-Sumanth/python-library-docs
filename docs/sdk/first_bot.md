# Build Your First Bot (Using Templates)

This guide will walk you through creating a bot by leveraging the power of **Templates**. Instead of writing complex logic from scratch, we will extend the existing `Forager` template to get a fully functional bot in just a few lines of code.

## 1. Setup
Create a new file `user.py` in your project root.

## 2. Using the Forager Template
The `seamaster` library includes several pre-built templates. The **Forager** is an advanced bot that handles:
*   **Harvesting**: Automatically finding and collecting algae.
*   **Banking**: Depositing resources when inventory is full.
*   **Charging**: Finding energy pads when low on energy.

Instead of re-writing this logic, we just import it!

```python linenums="1" hl_lines="1 3 9 31"
from seamaster.templates.forager import Forager  # (1)

class MyFirstBot(Forager):  # (2)
    """
    My custom bot that extends the Forager template.
    We can add custom logic here later, but for now, 
    we just rely on the robust Forager behavior.
    """
    def __init__(self, ctx, args=None):
        super().__init__(ctx, args)
        # Custom initialization can go here

    def act(self):
        # run the default forager logic
        return super().act()  # (3)

def spawn_policy(api):
    """
    Called every tick. Returns a list of spawn commands.
    """
    spawns = []
    
    # Check if we can afford to spawn (using the parent class method)
    if MyFirstBot.can_spawn(api):  # (4)
        spawns.append({
            "strategy": MyFirstBot,
            "location": 0, # Spawn at y=0
            "extra_abilities": []
        })
        
    return spawns
```

1.  **Import**: Bring in the pre-built `Forager` template to start with distinct behaviors.
2.  **Inherit**: Subclassing `Forager` gives your bot all its state machine logic (harvesting, banking, charging) for free.
3.  **Extend**: You can add your own logic before calling `super().act()`, or replace it entirely.
4.  **Spawn**: Use the template's helper method to check if you have enough energy to pay the spawn cost.

## 4. Running Your Bot
Submit the final code by pasting it into the editor in the battle page.mk

## What just happened?
By inheriting from `Forager`, your bot immediately gained the following behaviors:

1.  **Automated State Machine**: The bot automatically switches between three internal states:
    *   **Active**: Hunts for visible algae and scraps. It scans an expanding radius (from 2 up to 10 tiles) to find the nearest resource and moves towards it.
    *   **Depositing**: When the bot collects **5+ algae**, it automatically locks onto the nearest Bank and moves to deposit.
    *   **Charging**: If energy drops below **10 units**, it pauses harvesting and routes to the nearest Energy Pad to recharge.

2.  **Smart Navigation**: It uses `ctx.move_target()`, which leverages pre-computed shortest paths to navigate around walls. It also performs collision checking to ensure it doesn't walk into walls or other bots.

3.  **Economic Logic**: It completes the full economic loop (Harvest -> Bank -> Recharge) without you writing a single line of logic. Default abilities (`HARVEST`, `SCOUT`, `DEPOSIT`) are automatically configured.

## Customizing Your Bot
Now that you have a working base, you can override `act()` to add specific behaviors:

```python linenums="29" hl_lines="6"
    def act(self):
        # 1. Custom: If we see an enemy, maybe run away?
        enemies = self.ctx.sense_enemies()
        if enemies:
             # Add custom flee logic here
             pass

        # 2. Fallback to default Forager behavior
        return super().act()
```

## Conclusion
Building a Forager is just the beginning. The `seamaster` library is designed with a **flexible hierarchy**, giving you complete control over your bot's behavior.

You can mix and match templates, override specific methods (like `act`, `move_target`, or `can_spawn`), or build entirely new strategies from the ground up by inheriting directly from `BotController`. Whether you're optimizing for economy, aggression, or pure survival, the library provides the building blocks—how you assemble them is limited only by your imagination.

