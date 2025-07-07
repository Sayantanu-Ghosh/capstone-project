# capstone-project
✅ Brief Overview of the Project
This project implements a dynamic pricing model for a smart parking system using Pathway (a stream data processing library). It takes in real-time data streams of parking events and computes optimal parking prices by analyzing:

Occupancy levels

Traffic conditions

Vehicle type

Queue length

Special event days

The final output is a dynamically computed price per vehicle, which is updated in real-time and can be stored for visualization or further use.

🔧 Tech Stack Used
Python

Pandas (for initial data cleaning)

Pathway (for streaming, transformation, and real-time computation)

JSONLines (output storage format)

Optional: Mermaid for architecture visualization

🧠 Detailed Explanation of Project Architecture and Workflow
🔹 1. Data Cleaning & Preprocessing (Batch)
Raw CSV file (dataset.csv) contains timestamp in two columns: LastUpdatedDate and LastUpdatedTime.

These are combined to a single datetime column (Timestamp).

The final cleaned dataset contains:

Timestamp, Occupancy, Capacity, QueueLength, TrafficConditionNearby, IsSpecialDay, VehicleType

This cleaned file is saved as parking_stream_clean.csv.

🔹 2. Ingestion Using Pathway
A schema is defined using Pathway to assign correct data types.

Data is replayed at a defined rate (1000 rows/sec) from the cleaned CSV, simulating a streaming environment using pw.demo.replay_csv.

🔹 3. Feature Engineering Using UDFs (User Defined Functions)
▫️ traffic_level_mapper()
Converts text traffic levels (low, medium, high) into numerical values (0–2).

▫️ vehicle_weight()
Assigns a weight to each vehicle type:

car: 1.0

bike: 0.5

truck: 2.0

▫️ compute_demand()
Calculates a demand score based on:

Parking occupancy and capacity

Queue length

Traffic level

Whether it's a special day

Vehicle weight

▫️ demand_to_price()
Normalizes demand and computes price using this formula:

ini
Copy code
price = BASE_PRICE * (1 + LAMBDA * normalized_demand)
Ensures the price stays within bounds:

Min price: 5.0

Max price: 20.0

Base price: 10.0

🔹 4. Multi-Stage Stream Processing Pipeline
✅ Stage 1: Feature Extraction
Adds computed fields:

TrafficLevel

VehicleWeight

✅ Stage 2: Demand Calculation
Uses above features to calculate Demand.

✅ Stage 3: Dynamic Pricing
Uses Demand to compute final Price.

Adds Timestamp, t, and day for visualization.
<pre> \```mermaid flowchart LR A[Raw CSV Data] --> B[Data Cleaning (Pandas)] B --> C[Pathway Streaming Engine] C --> D1[Map Traffic & Vehicle Weight] D1 --> D2[Compute Demand Score] D2 --> D3[Dynamic Pricing Logic] D3 --> E[Final Output as JSONL] \``` </pre>
