# System Design for Proximity Service (Yelp-like Application)

## Initial Conversation: Scope Clarification
The design process begins with an exploratory conversation to establish the **scope** and **key functionalities** of the service. The exchange between the candidate and the interviewer focuses on defining the search radius, the user's ability to modify this radius, and the system's real-time data update capabilities.

### Key Dialogue Points
- **Search Radius**: Can users set the search radius, and if so, how does the system adapt if no businesses are found within the given radius?
- **Maximum Radius**: What's the maximum search radius allowed by the system?
- **User Interface Interaction**: How does the user interface allow changes to the search radius?
- **Real-time Data**: How are business information additions, deletions, and updates handled by the system?

## Functional Requirements
These requirements define what the system should do, outlining the core functionalities that enable users to interact with the service effectively.

1. **Proximity Searches**: Users should be able to search for businesses based on their geographic location.
2. **Dynamic Data**: The system should support the addition, deletion, and updating of business information without needing real-time updates for the user end.
3. **Business Information Access**: Detailed information about businesses must be accessible to users.

## Non-Functional Specifications
These specifications underpin the architecture's quality attributes, not directly tied to functionality but crucial for system effectiveness and customer satisfaction.

- **Scalability and High Availability**: Foremost, the architecture must sustain a robust performance under varying load, maintaining high service availability during demand surges, characteristic of urban centers and special events.
- **Performance Efficiency**: Response times must be minimal, as the utility of the service is highly dependent on its ability to present local business information promptly.
- **Security and Privacy Compliance**: Protecting user data is paramount. The system must enforce stringent measures to safeguard location data, adhering to international standards such as GDPR and local legislations like CCPA.
- **Reliability**: The service is expected to operate consistently, with a resilient design that mitigates and recovers swiftly from failures.

Incorporating these foundational specifications, we create a robust groundwork for diving into the architecture's detailed system design and API construction.

## GraphQL API Design 

Using [**GraphQL**](https://graphql.org/learn/), we construct a more efficient, flexible API by allowing clients to specify exactly what data they need, reducing over-fetching and under-fetching issues common in [**RESTful**](https://www.redhat.com/en/topics/api/what-is-a-rest-api) services.

## Schema Definition

We could potentially autogenerate this GraphQL schema by using code generation. I mean, this is the only way. Why would we want to have to manually keep track of and update this schema 🙅‍♂️. This approach ensures our schema definition stays in sync with the underlying data models and resolvers without manual updates. Various tools and libraries can introspect our data models and business logic to create or update the schema accordingly.
[Example of Popular CodeGen Libirary.](https://www.apollographql.com/tutorials/lift-off-part1/09-codegen)

### Types

```graphql
type Business {
  business_id: ID!
  address: String!
  city: String!
  state: String!
  country: String!
  location: Location!
}

type Location {
  latitude: Float!
  longitude: Float!
}

# The connection type wraps around the Business type to include pagination details
type BusinessConnection {
  edges: [BusinessEdge]
  pageInfo: PageInfo
}

type BusinessEdge {
  cursor: String
  node: Business
}

type PageInfo {
  endCursor: String
  hasNextPage: Boolean
}

type Query {
  # Query to get detailed information about a business
  business(business_id: ID!): Business

  # Query to get a list of businesses based on location and optional filters
  # with cursor-based pagination
  searchNearby(
    latitude: Float!,
    longitude: Float!,
    radius: Int!,
    after: String,
    first: Int
  ): BusinessConnection
}

type Mutation {
  # Mutation to create a new business
  createBusiness(input: BusinessInput!): Business

  # Mutation to update an existing business
  updateBusiness(business_id: ID!, input: BusinessInput!): Business

  # Mutation to delete a business
  deleteBusiness(business_id: ID!): Boolean
}

# Input types define the structure for the mutations
input BusinessInput {
  address: String!
  city: String!
  state: String!
  country: String!
  location: LocationInput!
}

input LocationInput {
  latitude: Float!
  longitude: Float!
}

# Filters for the search query can include various parameters
input SearchFilters {
  category: String
  rating: Float
  # Any additional filters can be included as needed
}

```

### Cursor-based Pagination Explained

[Cursor-based pagination, also known as 'relay-style' pagination](https://relay.dev/graphql/connections.htm), is a method of incrementally loading data, optimal for lists that are dynamic. It improves upon traditional page-based pagination by using a 'cursor'—a pointer to a specific place in a dataset—to fetch subsequent sets of results.

In our LBS (Location Based Service), here's how cursor-based pagination works:

1. **Initial Request**: Users search for nearby businesses, and the server returns the first 'n' results plus a cursor associated with the last item.
2. **Subsequent Requests**: For more results, users request the next set using the last cursor. The server returns items following that cursor, avoiding duplicates or missed items due to list changes.
3. **Continuous Experience**: If the list of businesses updates (additions, deletions), cursor-based pagination ensures users won't see duplicates or miss new entries.

**Performance Benefits:**

- **Efficiency**: Cursor-based pagination can be more efficient for the server and database. It avoids over-fetching data and the 'phantom reads' that can occur with page-based pagination in dynamic datasets.
- **Server Load**: This method can reduce server load by delivering only immediately necessary data, which is crucial under high traffic.
- **Database Queries**: Fetching data after a specific cursor (especially an indexed one) is often faster and less resource-intensive than calculating offsets for pages, as commonly done in page-based pagination.

For dynamic and extensive datasets, like those seen in proximity services, cursor-based pagination ensures users experience a smooth, consistent browsing of data without the risk of encountering duplicates or missing new entries. It also allows for more predictable performance from both the server's and database's perspective.

#### Business Query with Cursor-based Pagination

```graphql
query SearchNearbyBusinesses($latitude: Float!, $longitude: Float!, $radius: Int!, $after: String, $first: Int) {
  searchNearby(
    latitude: $latitude,
    longitude: $longitude,
    radius: $radius,
    after: $after,
    first: $first
  ) {
    edges {
      cursor
      node {
        business_id
        address
        city
        state
        country
        location {
          latitude
          longitude
        }
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```
