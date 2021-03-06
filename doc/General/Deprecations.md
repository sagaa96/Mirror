# Deprecations

Certain features of Unity Networking were removed from Mirror for various reasons. This page will identify all removed features, the reason for removal, and possible alternatives.

## Match Namespace

As part of the Unity Services, this entire namespace was removed. It didn't work well to begin with, and was incredibly complex to be part of the core networking package. We expect this, along with other back-end services, will be provided through standalone apps that have integration to Mirror.

## Network Discovery

NetworkDiscovery was a UNet component intended for UDP projects. Since Mirror was built on TCP, it was removed. Now that all [transports](../Transports/index.md) are separate components, Discovery has been re-implemented in at least one of them.

## networkPort in Network Manager

Network Manager's `networkPort` property was removed now that all transports are separate components. Not all transports use ports, but those that do have a field for it. See [Transports](../Transports/index.md) for more info.

## playerController in NetworkConnection

This was renamed to `identity` since that's what it is: the `NetworkIdentity` for the connection. If you need to convert a project after this change, Visual Studio / VS Code can help...read more [here](PlayerControllerToIdentity.md).

## Local Player Authority in NetworkIdentity

This has been phased out through overhauling, and simplifying how [Authority](../Guides/Authority.md) works in Mirror.

## Network Server Simple

This was too complex and impractical to maintain for what little it did, and was removed. There are much easier ways to make a basic listen server, with or without one of our transports.

## Couch Co-Op

The core networking was greatly simplified by removing this low-hanging fruit. It was buggy, and too convoluted to be worth fixing.  For those that need something like this, consider defining a non-visible player prefab as a message conduit that spawns actual player prefabs with client authority.  All inputs would route through the conduit prefab to control the player objects.

## Network Transform

This component was significantly simplified so that it only syncs position and rotation. Rigidbody support was removed. We will create a new separate component for NetworkRigidbody that will be server authoritative with physics simulation and interpolation.

## Quality of Service Flags

In classic UNET, QoS Flags were used to determine how packets got to the remote end. For example, if you needed a packet to be prioritized in the queue, you would specify a high priority flag which the Unity LLAPI would then receive and deal appropriately. Unfortunately, this caused a lot of extra work for the transport layer and some of the QoS flags did not work as intended due to buggy or code that relied on too much magic.

In Mirror, QoS flags were replaced with a "Channels" system. This system paves the way for future Mirror improvements, so you can send data on different channels - for example, you could have all game activity on channel 0, while in-game text chat is sent on channel 1 and voice chat is sent on channel 2. In the future, it may be possible to assign a transport system per channel, allowing one to have a TCP transport for critical game network data on channel 0, while in-game text and voice chat is running on a UDP transport in parallel on channel 1. Some transports (such as Ignorance.md) also provide legacy compatibility for those attached to QoS flags.

The currently defined channels are:

-   `Channels.DefaultReliable = 0`

-   `Channels.DefaultUnreliable = 1`

Currently, Mirror using it's default TCP transport will always send everything over a reliable channel. There is no way to bypass this behaviour without using a third-party transport, since TCP is always reliable. Other transports may support other channel sending methods.
