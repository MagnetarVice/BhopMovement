using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    private float accel = 200f; // Oyuncunun zemindeyken ne kadar hızlı hızlanabileceği
    private float airAccel = 200f; // Oyuncunun havada ne kadar hızlı hızlanabileceği

    [SerializeField] private float maxSpeed = 6.4f; // Oyuncunun zemindeyken maksimum hızı
    [SerializeField] private float maxAirSpeed = 0.6f; // Oyuncunun havadaki maksimum hızı

    [SerializeField] private float friction = 8f; // Oyuncunun zemindeyken ne kadar hızlı yavaşlayabileceği
    [SerializeField] private float jumpForce = 5f; // Oyuncunun zıplama yüksekliği

    [SerializeField] private GameObject camObj;
    [SerializeField] private LayerMask groundLayers;

    private float lastJumpPress = -1f;
    private float jumpPressDuration = 0.1f;
    private bool onGround = false;

    private void Update()
    {
        print(new Vector3(GetComponent<Rigidbody>().velocity.x, 0f, GetComponent<Rigidbody>().velocity.z).magnitude);
        if (Input.GetButtonDown("Jump"))
        {
            lastJumpPress = Time.time;
        }
    }

    private void FixedUpdate()
    {

        Vector2 input = new Vector2(Input.GetAxisRaw("Horizontal"), Input.GetAxisRaw("Vertical"));

        // Get player velocity
        Vector3 playerVelocity = GetComponent<Rigidbody>().velocity;
        // Slow down if on ground
        playerVelocity = CalculateFriction(playerVelocity);
        input = input.normalized;
        // Add player input
        playerVelocity += CalculateMovement(input, playerVelocity);

        // Assign new velocity to player object
        GetComponent<Rigidbody>().velocity = playerVelocity;
    }

	private Vector3 CalculateFriction(Vector3 currentVelocity)
    {
        onGround = CheckGround();
        float speed = currentVelocity.magnitude;

        if (!onGround || Input.GetButton("Jump") || speed == 0f)
            return currentVelocity;

        float drop = speed * friction * Time.deltaTime;
        return currentVelocity * (Mathf.Max(speed - drop, 0f) / speed);
    }

    private Vector3 CalculateMovement(Vector2 input, Vector3 velocity)
    {
        onGround = CheckGround();

        //Different acceleration values for ground and air
        float curAccel = accel;
        if (!onGround)
            curAccel = airAccel;

        //Ground speed
        float curMaxSpeed = maxSpeed;

        //Air speed
        if (!onGround)
            curMaxSpeed = maxAirSpeed;

        Vector3 camRotation = new Vector3(0f, camObj.transform.rotation.eulerAngles.y, 0f);
        Vector3 inputVelocity = Quaternion.Euler(camRotation) *
        new Vector3(input.x * curAccel, 0f, input.y * curAccel);
        Vector3 alignedInputVelocity = new Vector3(inputVelocity.x, 0f, inputVelocity.z) * Time.deltaTime;
        Vector3 currentVelocity = new Vector3(velocity.x, 0f, velocity.z);
        float max = Mathf.Max(0f, 1 - (currentVelocity.magnitude / curMaxSpeed));
        float velocityDot = Vector3.Dot(currentVelocity, alignedInputVelocity);
        Vector3 modifiedVelocity = alignedInputVelocity * max;
        Vector3 correctVelocity = Vector3.Lerp(alignedInputVelocity, modifiedVelocity, velocityDot);

        //Apply jump
        correctVelocity += GetJumpVelocity(velocity.y);

        //Return
        return correctVelocity;
    }

	private Vector3 GetJumpVelocity(float yVelocity)
    {
        Vector3 jumpVelocity = Vector3.zero;

        if (Time.time < lastJumpPress + jumpPressDuration && yVelocity < jumpForce && CheckGround())
        {
            lastJumpPress = -1f;
            jumpVelocity = new Vector3(0f, jumpForce - yVelocity, 0f);
        }

        return jumpVelocity;
    }

    #region CheckGround
    private bool CheckGround()
    {
        Ray ray = new Ray(transform.position, Vector3.down);
        bool result = Physics.Raycast(ray, GetComponent<Collider>().bounds.extents.y + 0.1f, groundLayers);
        return result;
    }
    #endregion 
}
