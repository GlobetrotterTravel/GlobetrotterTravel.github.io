# Globetrotter Iris

Globetrotter Iris is a scalable, flexible and reliable solution to managing service configuration data for organisations of any size. Iris is an API-first platform that is designed to fit into your workflows and deliver the benefits of centralised control and audit of complex information.

Iris manages objects called `components`, which when combined in a structured manner, represent the service configuration for all of your clients and partners. The principle `component` is `entity`, to which all other `components` plug into.

Iris serves *your data your way* to `subscriber` systems, giving you the confidence that all parts of your infrastructure are seeing the same source of truth. `Subscribers` can access a little or a lot, based on a strong security model and granular `permissions`. Integrate with both internal systems as well as partners and customers, taking advantage of industry REST API standards.

The following sections detail how to administer the different parts of Iris.

- [Getting Started](Getting%20Started.html)
- [System Journal](System%20Journal.html)
- [Properties](Properties.html)
- [Users, Roles and Permissions](Users,%20Roles%20and%20Permissions.html)
- [Entities](Entities.html)
- [Facets](Facets.html)
- [Entitlements](Entitlements.html)
- [Assets](Assets.htm)
- [Fields](Fields.html)
- [Hybrid Lists](Hybrid%20Lists.html)
- [Integration with Iris](Integration%20with%20Iris.html)

There is also a [Glossary of Terms](Glossary.html) available.

## Overview and key concepts

### Design philosophy

Iris has several design philosophies that are reflected in its behaviour. These are described below.

1. **Empowerment Through Data Ownership**: Iris is designed to put each organisation in control of their environment. By allowing flexible configuration of entities, fields, and facets, Iris ensures that organisations can customize their data and workflows to fit their exact requirements, rather than adapting their needs to a rigid system. This approach prioritizes organisation autonomy, giving each organisation the freedom to shape their environment as needed.

2. **Structured Flexibility for Tailored Solutions**: Iris recognizes that while a structured foundation is essential for consistency, every organisation’s needs are unique. The system is purposefully built to balance these two forces: it provides a well-defined framework with components like facet packages and definitions, which organisations can extend to meet specific requirements. This flexibility within a structured model ensures that customisations are both powerful and sustainable, reducing complexity while enabling tailored solutions.

3. **Modularity for Clarity and Ease of Use**: Iris’s modular design reflects a commitment to simplicity and clarity. By organizing components into distinct categories—entities, fields, facets, and hybrid lists—Iris breaks down complex configurations into manageable parts. This modularity enables organisations to understand and interact with the system in a way that feels intuitive, making it easier to implement, update, and scale their configurations over time. The goal is to minimize complexity and streamline workflows, allowing users to focus on meaningful data without being overwhelmed.

3. **Controlled Customisation for Consistency and Flexibility**: Iris is built to support standardized configurations across organisations while allowing for targeted customisation where needed, providing a shared foundation of data while enabling organisations to make adjustments at the entity level. By giving organisations the ability to align their configurations to a common standard yet selectively customize aspects of their entities, Iris reduces complexity and ensures data consistency. This controlled customisation approach empowers organisations to maintain uniformity in core configurations while adapting specific elements to meet unique organisational requirements, preserving clarity and manageability as the system scales.

4. **Embedded Knowledge Through Self-Documentation**: Iris values transparency and continuity of knowledge. By embedding documentation directly within the system, organisations can keep track of the purpose and context of their configurations. This self-documenting approach ensures that every configuration choice has context readily available, supporting informed decision-making and reducing reliance on external documentation. This principle makes the system itself a single source of truth, enhancing long-term understanding and collaboration across teams.

5. **Real-World Alignment in Structure and Relationships**: Iris is designed to reflect real-world organisational structures and relationships. Entities can be configured with parent-child hierarchies, and components like assets and entitlements are clearly linked to the relevant entities. This design mirrors how information is organized in actual business environments, making it intuitive for organisations to map their data to Iris. By aligning with natural organisational relationships, Iris enhances usability and helps organisations quickly grasp how to represent their own structure within the system.

6. **Consistency Without Conformity**: Iris encourages consistency across organisation configurations without enforcing a one-size-fits-all model. While shared components and structures provide a familiar and standardized foundation, organisations are empowered to configure their own data models uniquely. This philosophy respects the individuality of each organisation’s operations while maintaining a consistent and cohesive experience across the platform, fostering a flexible yet unified approach to managing data and configurations.

These principles reflect a system built for adaptability, security, and ease of use, supporting a structured yet customisable approach to service configuration management.

## REST API platform

Iris is built around a REST API architecture, that serves `subscribers` with data in response to their requests.

The base URL for accessing Iris via REST API is:

```url
/tenant/{tenantId}/api/vx
```

Where **vx** represents an integer version number, such as **v1**.

Iris expects the tenant id to be passed on the URL path in every request.

There is also another namespace, **dapi** which is used for dynamic endpoints. Its base URL is in a simlilar format:

```url
/tenant/{tenantId}/dapi
```

The **dapi** namespace makes it clear that the `subscriber` is accessing a dynamically generated endpoint. See the section on [dynamic endpoints](Dynamic%20Endpoints.md) for more information.

## Components

These are the Iris components:

* Entity
* Field
* Hybrid List
* Entitlement
* Asset

Within each component, administrators create `classes` and within `classes`, `definitions`. `Classes` serve to logically group different `component` `definitions` for management purposes, while `definitions` allow administrators to create a library of very specific component configurations that can be related to one another in a structured manner. It is these `definitions` that form the backbone of Iris' flexible structure.

### Instantiating entities

Instantiating an `entity` simply means creating a new record of that `entity's` particular `definition`. Because administrators create organisation-specific `definitions` for the above `components`, `components` can't be used directly. Instead, new `instances` of the `definitions` are created to hold the information needed.

Because `entity` is the primary component that Iris manages, it's the only component that can be directly instantiated, and this occurs when Iris receives a request for a new `entity` record along with an entity `definition` to use.

When an `entity's` `definition` is instantiated, the `instance` automatically gets further `instances` of the `definition's`:

* **facet package**
* **field package**, including any facet schemas attached to the field's definition
* **hybrid list package**, including any facet schemas attached to the hybrid list's definition
* **entitlements package**, including any facet schemas attached to the entitlement's definition
* **assets package**, including any facet schemas attached to the asset's definition

