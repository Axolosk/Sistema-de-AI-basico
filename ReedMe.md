# AI  De  Enemigo Basica

# Creador: Oskar Dimas

## Introducion

La inteligencia artificial básica es fundamental en un videojuego, ya que permite que los enemigos tengan comportamientos predecibles y desafiantes para el jugador. Esto ayuda a mejorar la experiencia del juego y a mantener al jugador interesado y comprometido con el juego.

continuando con los codigos que encontraremos en el proyecto tenemos script mas importante, Enemigo 

public class Enemigo : MonoBehaviour
{
// variables que tomara el enemigo al caminar sin el objetivo en rango
public int rutina;
public float cronometro;
public Animator ani;
public Quaternion angulo;
public float grado;

```
//objetivo
public GameObject target;
public bool atacando;

// Start is called before the first frame update
void Start()
{
    ani = GetComponent<Animator>();
    //target = GameObject.Find("Target");

}

public void Comportamiento()
{
    if (Vector3.Distance(transform.position, target.transform.position) > 9.5)
    {

        ani.SetBool("run", false);
        cronometro += 1 * Time.deltaTime;
        if (cronometro >= 4)
        {
            rutina = Random.Range(0, 2);
            cronometro = 0;
        }
        switch (rutina)
        {
            case 0:
                ani.SetBool("walk", false);
                break;

            case 1:
                grado = Random.Range(0, 360);
                angulo = Quaternion.Euler(0, grado, 0);
                rutina++;
                break;

            case 2:
                transform.rotation = Quaternion.RotateTowards(transform.rotation, angulo, 0.5f);
                transform.Translate(Vector3.forward * 1 * Time.deltaTime);
                ani.SetBool("walk", true);
                break;

        }

    }
    else
    {
        var lookPos = target.transform.position - transform.position;
        lookPos.y = 0;
        var rotation = Quaternion.LookRotation(lookPos);

        if (Vector3.Distance(transform.position, target.transform.position) > 2 && !atacando)
        {

            transform.rotation = Quaternion.RotateTowards(transform.rotation, rotation, 2);
            ani.SetBool("walk", false);

            ani.SetBool("run", true);
            transform.Translate(Vector3.forward * 2 * Time.deltaTime);

            ani.SetBool("attack", false);

        }
        else
        {
            transform.rotation = Quaternion.RotateTowards(transform.rotation, rotation, 2);

            ani.SetBool("walk", false);
            ani.SetBool("run", false);

            ani.SetBool("attack", true);
            atacando = true;
        }

    }

}

public void Final_Ani()
{
    ani.SetBool("attack", false);
    atacando = false;

}

// Update is called once per frame
void Update()
{
    Comportamiento();

}

```

}

Este es un script para el comportamiento de un enemigo en un videojuego. En el comienzo del código, se definen las variables que el enemigo seguirá mientras camina sin un objetivo en su rango. Luego, se define el objetivo del enemigo y se comprueba si está atacando.

El método `Comportamiento()` es el núcleo de este script. Si el objetivo no está dentro del rango del enemigo, el enemigo no se moverá, pero cambiará su comportamiento de caminar en intervalos aleatorios. Si el objetivo está dentro del rango, el enemigo se moverá hacia él y lo atacará. El enemigo ajustará su animación de caminar mientras se mueve y rotará para enfrentarse al objetivo.

El script utiliza la función `Quaternion.Euler()` para definir un ángulo de rotación aleatorio y la función `Quaternion.RotateTowards()` para rotar el enemigo hacia ese ángulo. También utiliza la función `Vector3.Distance()` para medir la distancia entre el enemigo y su objetivo.

Siguiendo con Player:

public class Player : MonoBehaviour
{
         public Rigidbody rb;
         public float speed;
         public Animator ani;
public Transform Eje;

```
public bool inground;
private RaycastHit hit;
public float distance;
public Vector3 v3;

void Move()
{
    Vector3 RotaTargetZ = Eje.transform.forward;
    RotaTargetZ.y = 0;

    if (Input.GetKey(KeyCode.W))
    {
        transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(RotaTargetZ), 0.3f);
        var dir = transform.forward * speed * Time.fixedDeltaTime;
        dir.y = rb.velocity.y;
        rb.velocity = dir;
        ani.SetBool("run", true);

    }
    else
    {
        if (inground)
        {
            rb.velocity = Vector3.zero;

        }
        ani.SetBool("run", false);

    }

    if (Input.GetKey(KeyCode.S))
    {
        transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(RotaTargetZ * -1), 0.3f);
        var dir = transform.forward * speed * Time.fixedDeltaTime;
        dir.y = rb.velocity.y;
        rb.velocity = dir;
        ani.SetBool("run", true);
    }

    Vector3 RotaTargetX = Eje.transform.right;
    RotaTargetX.y = 0;

    if (Input.GetKey(KeyCode.D))
    {
        transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(RotaTargetX), 0.3f);
        var dir = transform.forward * speed * Time.fixedDeltaTime;
        dir.y = rb.velocity.y;
        rb.velocity = dir;
        ani.SetBool("run", true);

    }

    if (Input.GetKey(KeyCode.A))
    {
        transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(RotaTargetX * -1), 0.3f);
        var dir = transform.forward * speed * Time.fixedDeltaTime;
        dir.y = rb.velocity.y;
        rb.velocity = dir;
        ani.SetBool("run", true);
    }

}

void FixedUpdate()
{
    Move();
}

private void Update()
{

    if (Physics.Raycast(transform.position +v3, transform.up *-1, out hit, distance))
    {
        if (hit.collider.tag == "piso")
        {
            inground = true;
        }

    }
    else
    {
        inground = false;
    }
}

private void OnDrawGizmos()
{

    Gizmos.DrawRay(transform.position + v3, Vector3.up * -1 * distance);
}

```

}

Este código es un script de movimiento para un personaje en un videojuego. Permite al personaje moverse hacia adelante (**W**), hacia atrás (**S**), a la izquierda (**A**) y a la derecha (**D**) en respuesta a las teclas presionadas por el jugador. También cambia la animación del personaje dependiendo de la dirección en la que se mueve. Utiliza la física de `Rigidbody` para simular la gravedad y la detección de colisiones con `RaycastHit`. 

Continuando con el codigo que ira de la mano con el codigo player tenemos a  Camera:

public class Camera : MonoBehaviour
{
public GameObject target;
public Vector3 V3;
public float speed;

```
public float maxLook;
public float minLook;
public Quaternion CamRotation;

void Start()
{
    CamRotation = transform.localRotation;
}

public void Cam()
{
    if (target)
    {
        transform.position = target.transform.position + V3;
    }

    CamRotation.y += Input.GetAxis("Mouse X") * speed;
    CamRotation.y += Input.GetAxis("Mouse Y") * speed * -1;

    CamRotation.x = Mathf.Clamp(CamRotation.x, minLook, maxLook);

    transform.localRotation = Quaternion.Euler(CamRotation.x, CamRotation.y, CamRotation.z);
}

void Update()
{
    Cam();
}

```

}

Este código es un script para una cámara en un videojuego. La cámara seguirá al objeto definido por la variable `target` y se colocará a una distancia predefinida de él.

El método `Cam()` es el núcleo de este script. En el método, la posición de la cámara se define como la posición del objeto `target` más un vector predefinido llamado `V3`. Luego, el script ajusta la rotación de la cámara en respuesta al movimiento del ratón del jugador utilizando las funciones `Input.GetAxis()`. El script utiliza la función `Mathf.Clamp()` para limitar la rotación vertical de la cámara entre dos valores predefinidos.

El script utiliza la variable `CamRotation` para almacenar la rotación actual de la cámara y la función `Quaternion.Euler()` para aplicar esta rotación a la cámara. La función `transform.localRotation` se utiliza para aplicar la rotación localmente a la cámara.

En resumen, este script permite que la cámara siga a un objeto en un videojuego y se adapte a los movimientos del ratón del jugador para ajustar su rotación.