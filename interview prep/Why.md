


- Why are you seeking a new role? (Clarify your motivations and make sure they are genuine. Don’t discuss working this role alongside your current)

Mention that this is your passion , you think EV chargers is future , ocpp is new thing and will grow and i want to get as much expereince as possible in this domaine



 
- What excites you about a potential new employer? (Make it relevant to enkel energi and what they’re doing as a company, not just money or changing companies)
  
  So far i have build and re searched about ocpp protocl by myself alone , would be great to learn more about the subject from different persepcitves
  
  
  
  
  
  - Research **enkel energi** to understand who they are and what they do. Reference what you’ve learned in your research
  
  I’ve been digging into what you guys are building. It’s cool because you aren’t just another power company. You're basically taking thousands of cars that are just sitting there—**idle batteries**—and turning them into a massive, decentralized power plant. You're making the car 'smarter' than the grid itself.
  
  I’ve spent my career building the 'pipes'—the GPRS controllers, the meters, and the OCPP bridges. I want to work at Enkel because you are building the **brain**. I want to move from just _collecting_ telemetry to _using_ it to solve real-world energy market challenges
  
  
  
  - They will tell you about the projects, customer requirements, and look to understand where you have completed similar tasks in your career
    
    Monta is basically the "Operating System" for EV charging. They provide the software that connects chargers, drivers, and companies.
    
    
    
    I built a **Healer Service** because I realized that 'Faulted' isn't the only state that breaks the user experience. I saw chargers get stuck in `SuspendedEVSE` or `Finishing` for hours due to mechanical or software deadlocks.

My service monitors the **telemetry patterns**. For example, if a charger is sending heartbeats but ignoring `RemoteStart` commands, the healer identifies it as a 'Zombie' state and automatically issues a **Soft Reset**. It reduced our manual support tickets by [X]%, because the system fixes the 'stupid' hardware issues before the user even notices
    
    ### **1. The "SuspendedEVSE" Deadlock**

- **What it is:** The charger (EVSE) stops the flow of power, usually due to a smart charging limit or a temporary grid issue.
    
- **The Problem:** Sometimes the charger gets "stuck" in this state even after you send a new `SetChargingProfile`. The backend thinks it's fine, but the car isn't getting juice.
    
- **Healer Action:** If the status stays `SuspendedEVSE` for X minutes while a transaction is active and the grid limit has been lifted, your service should trigger a **Soft Reset**.
  
  
  ### **The "Stale" Preparing State**

- **What it is:** The user has plugged in, and the status changed to `Preparing`, but the transaction never starts (no `StartTransaction.conf`).
    
- **The Problem:** This often happens due to an authorization timeout or a communication glitch between the car and the charger (common in older ISO 15118 handshakes). The charger just sits there "waiting" forever and won't accept a new RFID or remote command.
    
- **Healer Action:** If `Preparing` lasts longer than 5 minutes without a transaction, the healer should **ClearCache** and then **Reset** to "unjam" the connector.