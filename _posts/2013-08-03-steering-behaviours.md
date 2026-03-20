---
layout: post
title: "Steering Behaviors"
date: 2013-08-06
last_modified_at: 2014-05-18
categories: [Artificial Intelligence, Geometry, Techniques]
tags: [artificial-intelligence, geometry, game-ai, steering-behaviors]
migrated_from: https://ahmedhanibrahim.wordpress.com/2013/08/06/steering-behaviors/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2013/08/06/steering-behaviors/) on August 6, 2013, and has been migrated here.

Steering behaviors are a collection of techniques that help autonomous characters move in a realistic manner, by combining forces that produce natural, believable movement.

This post covers the following behaviors:

- **Seek**
- **Flee and Arrival**
- **Wandering**
- **Pursuit and Evade**

---

## Seek

![Seek behavior diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test.png)

The direction of the velocity controls where the plane is heading, while its length controls how much it moves every frame — the greater the length, the faster the plane moves.

To calculate the desired velocity vector that drives the plane toward its target:

```csharp
Vector3 Normal = Vector3.Normalize(GameObject.Position - TargetPosition);
Vector3 DesiredVelocity = Normal * MaxVelocity;
```

Without a steering force, the plane would take straight routes, which isn't realistic. Adding a **steering force** allows the plane to smoothly adjust its velocity, avoiding sudden path changes when the target changes direction.

![Seek steering force diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test1.png)

The steering force is calculated as follows:

```csharp
Vector3 Normal = Vector3.Normalize(TargetPosition - GameObject.Position);
Vector3 DesiredVelocity = Normal * MaxVelocity;
return (DesiredVelocity - GameObject.LinearVelocity);
```

After accumulating these forces, the enemy follows a smooth path toward the player's position.

---

## Flee

Flee uses the same force mechanics as Seek, but instead drives the enemy **away** from the player.

![Flee behavior diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test2.png)

The desired velocity is computed by subtracting the target's position from the enemy's position, producing a vector that points away from the player:

```csharp
Vector3 Normal = Vector3.Normalize(GameObject.Position - TargetPosition);
Vector3 DesiredVelocity = Normal * MaxVelocity;
return (DesiredVelocity - GameObject.LinearVelocity);
```

> **Note:** `Flee Steering = -Seek Steering`

Combining these forces produces a natural fleeing path:

![Flee path diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test3.png)

---

## Arrival

Arrival prevents the enemy from overshooting the target by gradually slowing down as it enters a defined **slowing area** around the player, coming to a full stop at the target position.

This behavior has two phases:

1. **Outside the slowing area** — behaves like Seek, moving at full velocity.
2. **Inside the slowing area** — velocity decreases linearly to zero.

![Arrival behavior diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test4.png)

First, calculate the desired velocity and distance to the target:

```csharp
Vector3 DesiredVelocity = TargetPosition - GameObject.Position;
float Distance = DesiredVelocity.Length();
```

Then apply the arrival steering:

```csharp
if (Distance > 0)
{
    float Speed = (Distance / DecelerationVelocity * 0.3f);
    Speed = TruncateFloat(Speed, MaxVelocity);
    Vector3 DesiredVelocity = DistanceToTarget * Speed / Distance;
    return (DesiredVelocity - GameObject.LinearVelocity);
}
else
{
    return new Vector3();
}
```

> **Note:** The enemy's original velocity vector doesn't change directly — the arrival steering addition counteracts it, bringing it to zero inside the slowing area. Outside the slowing area, the original velocity is used as-is.

---

## Wandering

The wandering behavior produces a sense of autonomous, organic movement — making the player aware of a nearby enemy that is alive and unpredictable.

The idea is to apply a small random displacement to the enemy's current direction vector each frame. This prevents sudden direction changes, instead smoothly nudging the plane left or right.

A circle is projected in front of the enemy's plane, and a displacement force on its circumference is computed each frame:

![Wandering behavior diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test5.png)

Implementation steps:

1. Place a circle in front of the plane by copying and normalizing the velocity vector, then scaling it by a scalar offset.
2. Compute a 2D displacement force on the circle's circumference — a 2D vector is sufficient since wandering only concerns lateral movement (left/right).
3. A larger circle radius produces a stronger wandering effect.

```csharp
WanderTarget = new Vector2(RandomClamped() * WanderJitter, RandomClamped() * WanderJitter);
WanderTarget.Normalize();
WanderTarget *= WanderRadius;
Vector2 DesiredLocalTarget = WanderTarget + new Vector2(WanderDistance, 0);
```

---

## Pursuit and Evade

### Pursuit

Pursuit differs from Seek in that the pursuer doesn't target the enemy's **current** position — it predicts the enemy's **future position** and steers toward that instead.

![Pursuit behavior diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test6.png)

![Pursuit prediction diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test7.png)

The future position is estimated using **Euler integration**:

```
Position = Position + Velocity
```

With a look-ahead factor `N` (number of game updates):

```
Position = Position + Velocity * N
```

A higher `N` yields better prediction; `N` close to zero degrades to basic Seek.

Full pursuit implementation:

```csharp
Vector3 targetPos = aTarget.Position;

// Check if facing each other directly
Vector3 DistanceToVectorTarget = targetPos - GameObject.Position;
double MyHeading = Vector3.Dot(Heading, aTarget.Heading);
double TargetHeading = Vector3.Dot(aTarget.Heading, Heading);

if ((TargetHeading > 0) && (MyHeading < -0.95))
{
    return Seek(aTarget.Position);
}

float Speed = Magnitude(GameObject.LinearVelocity);

// Predict where the target will be and head there
float LookAheadTime = (DistanceToVectorTarget.Length() /
    (aTarget.SteeringBehaviors.MaxVelocityFunction + Speed));
LookAheadTime += TurnAroundTheTime(targetPos);

return Seek((targetPos + aTarget.LinearVelocity) * LookAheadTime);
```

---

### Evade

Evade is the inverse of Pursuit — the enemy flees from the **predicted future position** of the player.

![Evade behavior diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2013/08/test8.png)

The implementation mirrors Pursuit, substituting `Flee` for `Seek`:

```csharp
Vector3 targetPos = aTarget.Position;

// Check if facing each other directly
Vector3 DistanceToVectorTarget = targetPos - GameObject.Position;
double MyHeading = Vector3.Dot(Heading, aTarget.Heading);
double TargetHeading = Vector3.Dot(aTarget.Heading, Heading);

if ((TargetHeading > 0) && (MyHeading < -0.95))
{
    return Seek(aTarget.Position);
}

float Speed = Magnitude(GameObject.LinearVelocity);

// Predict where the target will be and flee from there
float LookAheadTime = (DistanceToVectorTarget.Length() /
    (aTarget.SteeringBehaviors.MaxVelocityFunction + Speed));
LookAheadTime += TurnAroundTheTime(targetPos);

return Flee((targetPos + aTarget.LinearVelocity) * LookAheadTime);
```

---

## Closing Thoughts

There are many more techniques in the steering behaviors family, but implementing the five covered here — Seek, Flee, Arrival, Wandering, and Pursuit/Evade — is sufficient to produce convincingly realistic movement for opponent units in most game scenarios.

---

## References

- Craig Reynolds, [Steering Behaviors For Autonomous Characters (GDC 1999)](http://www.red3d.com/cwr/steer/gdc99/)
