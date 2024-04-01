# System Design For Google Maps

## Understanding the Scope

> Grasping the scope of a problem is fundamental in system design and algorithmic challenges alike. This comprehension not only streamlines solution development but also aligns expectations with the interviewer's objectives. In essence, it serves as a compass for navigating complex problems, ensuring that solutions are both relevant and efficiently tailored to the task at hand. The interviewer's role is to guide this exploration, making clarifications and scope adjustments a critical part of the dialogue.

### A. User Base

- **Question:** What's the expected daily active user count for our system?
- **Answer:** Aim for 1 billion DAU.

### B. Core Features

- **Question:** Which key functionalities are we prioritizing?
- **Answer:** Focus on location updates, navigation, ETA, and map rendering.

### C. Data Sources and Volume

- **Question:** Is there a pre-established source for road data, and what's its volume?
- **Answer:** Yes, road data will be aggregated from multiple sources, totaling terabytes in size.

### D. Traffic Information

- **Question:** How integral are traffic updates to our system?
- **Answer:** Traffic conditions are crucial for providing accurate ETAs.

### E. Mode of Transportation

- **Question:** Are we considering various transportation modes in our design?
- **Answer:** Yes, the system should accommodate different modes of travel.

### F. Route Customization

- **Question:** Will the system allow for multi-stop route planning?
- **Answer:** Users can define stops, but it's not our primary focus.

### G. Additional Features

- **Question:** Are business listings and photographic data within our design scope?
- **Answer:** Those features are not part of this design phase.

## Functional Requirements

After asking the relevant questions, we've delineated the following functional requirements for the system design of Google Maps.
- **Navigation Service**: Providing real-time directions for various modes of transportation.
- **Estimated Time of Arrival (ETA)**: Calculating and updating ETA based on current traffic conditions and road data.
- **Map Rendering**: Displaying maps dynamically with the ability to zoom, pan, and view in different modes.
- **User Location Updates**: Tracking and updating user location in real-time to offer navigation and traffic updates.

## Non-functional Requirements

With the functional aspects defined, we shift focus to the non-functional requirements, crucial for ensuring the system's resilience, performance, and user satisfaction.

### CAP Theorem Considerations

In the context of the CAP theorem (Consistency, Availability, Partition Tolerance), our system prioritizes **Availability** and **Partition Tolerance**. Here's why:

- **Availability**: Given the global user base and the critical nature of navigation services, our system must ensure high availability. Users expect the service to be accessible at all times, irrespective of their location or network issues, making Availability paramount.

- **Partition Tolerance**: The distributed nature of our data centers, which store vast amounts of map and traffic data, necessitates a system design that tolerates network partitions. Our service must continue to operate even when some nodes or segments of the network are unable to communicate.

- **Consistency**: While important, strict consistency is less critical in our context. Map and road data do not change frequently, and users can tolerate slight delays in the reflection of such updates (eventual consistency). This approach allows us to maintain high availability and partition tolerance without significantly compromising the user experience.

### Data Accuracy

- **High Data Accuracy**: Despite the flexibility with consistency, the accuracy of the data we serve—especially directions and ETAs—must be impeccable. Incorrect data could lead to user frustration, safety issues, or trust erosion. We employ sophisticated algorithms and data validation techniques to ensure the highest accuracy.

### Smooth Navigation

- **Client-Side Experience**: The application must deliver seamless map rendering and navigation features. This includes fluid transitions, responsive interactions, and minimal latency, crucial for user satisfaction. Techniques like efficient data compression, pre-fetching map segments, and optimizing rendering algorithms are vital.

### Data and Battery Usage

- **Efficiency on User Devices**: Considering that our service will be used on mobile devices, it's essential to minimize data and battery consumption. This can be achieved through:
    - **Data Optimization**: Using efficient data formats and compression techniques to reduce the size of map downloads and updates.
    - **Battery Conservation**: Optimizing location tracking and map rendering processes to conserve battery life. This includes intelligent polling for location updates and leveraging device hardware effectively.

Our non-functional requirements are designed to support a highly available, partition-tolerant system that provides accurate data and a smooth user experience while minimizing data and battery usage on client devices.

## Map Details

### Interpolation Method
- **Usage**: The interpolation method is used when a user inputs an address into the system which needs to be translated into a geographic coordinate for mapping and navigation purposes.
- **Implementation**:
  - Backend Service: This is a server-side operation due to the computational and data requirements.
  - Data Sources: Utilizes GIS (Geographic Information System) data, which includes detailed street networks and associated geographic coordinates.
  - Process: When an address is received, the interpolation service calculates the approximate location by using known coordinates of the street segment and the numeric address range.
- **Services Utilizing Interpolation**:
  - **Search and Location Lookup**: When users search for an address or place, the interpolation system is queried to find the exact coordinates to display on the map.
  - **Navigation Initiation**: Before starting navigation, the starting or ending address provided by the user is converted to coordinates.

### Reverse Geocoding
- **Usage**: Reverse geocoding is used to convert geographic coordinates into a human-readable address. It's typically used when the user's location is known as a latitude/longitude pair, but a readable address is needed.
- **Implementation**:
  - Backend Service: Like geocoding, reverse geocoding is performed server-side due to the intensive data requirements.
  - Data Sources: References the same GIS street data but works in the opposite direction, matching coordinates to the nearest known address range.
  - Process: Takes the latitude and longitude provided, usually from a GPS or other location-detecting service, and finds the closest address within the street network.
- **Services Utilizing Reverse Geocoding**:
  - **Location Sharing**: When users want to share their current location, reverse geocoding provides an address rather than just coordinates.
  - **Location Tagging in Photos or Posts**: Apps can tag the user's location with a readable address in social media posts or photo metadata.
  - **Incident Reporting**: If a user reports a road incident within the app, reverse geocoding translates the incident location coordinates into an address for others to understand.

### Map Rendering
- **Tile-Based Rendering**: Breaks down the map into smaller, downloadable tiles for efficient loading and minimal bandwidth usage.
  - **Client-Side Experience**: Ensures seamless zoom and pan functionalities by preloading adjacent tiles and using caching strategies.

### Navigation Algorithms
- **Pathfinding**: Employs algorithms like Dijkstra's or A* for calculating the shortest path between two points considering various real-time factors like traffic.
  - **Real-Time Traffic**: Integrates live traffic data to update routes dynamically.
  - **Predictive Modelling**: Uses historical data to predict traffic conditions for future dates and times.

## Services

### Location Service

### Navigation Service
