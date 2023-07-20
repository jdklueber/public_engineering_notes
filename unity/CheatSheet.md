# Game Dev/Unity Cheat Sheet

## PARTICLE EFFECT ISN'T SHOWING?
* Make sure you have a material set for it.  Sprite/Default is probably what you're looking for.

## QUICK WAY TO MAKE A THING WALK TOWARD PLAYER
* compare player transform position x w/thing's x.  Set localscale's x to 1 or -1 appropriately
* Vector3.Movetoward (thing.position, player.position, speed * time.deltatime)

## Angle for rotation to target: 
1.  Find the Vector diff between self and target:
```cs
    Vector3 direction = transform.position - target.transform.position;
```

For example, given self = (2, 3) and target = (4, 5), the resultant Vector will be (2, 2).  

2. Calculate the angle to rotate:
```cs
    float angle = Mathf.Atan2(direction.y, direction.x) * Mathf.Rad2Deg;
```
This calculates the angle via the arctangent in radians, then converts that to degrees.

3. Build a Quaternion rotation:
```cs
Quaternion targetRot = Quaternion.AngleAxis(angle, Vector3.forward);
```
Forward in this case is the Z axis

4. Set rotation
transform.rotation = Quaternion.Slerp(transform.rotation, targetRot, turnSpeed * Time.deltaTime);

### OR you can use slerp to rotate slowly.
```cs
    public Transform sunrise;
    public Transform sunset;

    // Time to move from sunrise to sunset position, in seconds.
    public float journeyTime = 1.0f;

    // The time at which the animation started.
    private float startTime;

    void Start()
    {
        // Note the time at the start of the animation.
        startTime = Time.time;
    }

    void Update()
    {
        // The center of the arc
        Vector3 center = (sunrise.position + sunset.position) * 0.5F; //ie the average of the slopes

        // move the center a bit downwards to make the arc vertical
        center -= new Vector3(0, 1, 0);

        // Interpolate over the arc relative to center
        Vector3 riseRelCenter = sunrise.position - center;
        Vector3 setRelCenter = sunset.position - center;

        // The fraction of the animation that has happened so far is
        // equal to the elapsed time divided by the desired time for
        // the total journey.
        float fracComplete = (Time.time - startTime) / journeyTime;
        //                    elapsed time

        transform.position = Vector3.Slerp(riseRelCenter, setRelCenter, fracComplete);
        transform.position += center;
    }
```


## Basic Movement
1. Using velocity and physics:
```cs
    Rigidbody.velocity = new Vector2(Input.GetAxisRaw("Horizontal") * MovementSpeed, Rigidbody.velocity.y);

            if (Rigidbody.velocity.x < 0)
            {
                transform.localScale = new Vector3(-1, 1, 1); //turn left
            }
            else if (Rigidbody.velocity.x > 0)
            {
                transform.localScale = Vector3.one; //turn right
            } 
```

2.  Or, just moving the damned thing:
```cs
    //Given a current position, a target position, and a movement speed...
    //Don't forget to multiply speed by Time.deltaTime!!
    Boss.position = Vector3.MoveTowards(Boss.position, TargetPoint.position, PhaseTwoMoveSpeed * Time.deltaTime);
```

3.  If you want to do it in a certain number of frames and to hell with the speed:
```cs
public int interpolationFramesCount = 45; // Number of frames to completely interpolate between the 2 positions
    int elapsedFrames = 0;

    void Update()
    {
        float interpolationRatio = (float)elapsedFrames / interpolationFramesCount;

        Vector3 interpolatedPosition = Vector3.Lerp(Vector3.up, Vector3.forward, interpolationRatio);
        //Here, notice that you're getting the current position based on the start pos, end pos, and
        //the ratio between the current framecount and the intended framecount.

        elapsedFrames = (elapsedFrames + 1) % (interpolationFramesCount + 1);  
        // This is really clever, making use of int truncation by asking for the remainder between the
        // lower number and the higher one.  It'll always be the lower number UNTIL it hits the
        // higher number, making it 0 again.


        Debug.DrawLine(Vector3.zero, Vector3.up, Color.green);
        Debug.DrawLine(Vector3.zero, Vector3.forward, Color.blue);
        Debug.DrawLine(Vector3.zero, interpolatedPosition, Color.yellow);
    }
```