Aquí tienes las respuestas al cuestionario sobre "Herencia", adaptadas a un perfil con conocimientos de C/C++ y los temas previos de Java, redactadas en tono impersonal.

# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta
La **herencia** es un mecanismo estructural que permite crear una nueva clase (subclase o clase derivada) a partir de una clase existente (superclase o clase base). Esta relación establece semánticamente que la nueva clase es una versión más especializada de la original, conformando lo que se conoce como la relación "es-un" (*is-a*). Por ejemplo, un artillero "es un" soldado.

La primera implicación fundamental es la **compatibilidad de tipos**. Al establecer la herencia, el compilador garantiza que cualquier instancia de la subclase puede ser tratada e identificada como si fuera una instancia de la superclase. Esto equivale a la posibilidad en C de usar un puntero a una estructura genérica para apuntar a una estructura más específica, permitiendo manejar colecciones heterogéneas bajo un tipo común unificador.

La segunda implicación es la **herencia de estado y comportamiento**. La subclase incorpora automáticamente todos los atributos y métodos definidos en la superclase, sin necesidad de reescribir ese código. Se adquiere la base completa de la superclase y sobre ella se añaden los nuevos datos y funciones específicas de la especialización.

```java
// Clase base (Superclase)
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Se presenta el soldado " + nombre);
    }
}

// Subclase Artillero
class Artillero extends Soldado {
    private int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre); // Llamada al constructor de la clase base
        this.cohetes = cohetes;
    }

    public int getCohetes() { return cohetes; }
    
    public void dispararCohete() { System.out.println("Fiuuuu... Pum!"); }
}

// Subclase Zapador
class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() { return minas; }
    
    public void ponerMina() { System.out.println("Mina activada."); }
}

public class MainEjemplo {
    public static void main(String[] args) {
        // Compatibilidad de tipos: un array del tipo base acepta cualquier subtipo
        Soldado[] peloton = new Soldado[3];
        peloton[0] = new Soldado("Recluta Patoso");
        peloton[1] = new Artillero("Rambo", 5);
        peloton[2] = new Zapador("MacGyver", 10);

        // Recorrido unificado ignorando los detalles específicos
        for (int i = 0; i < peloton.length; i++) {
            peloton[i].saludar();
        }
    }
}
```

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre?

### Respuesta
Al instanciar un objeto de una subclase, se ejecutan en cascada los constructores de todas las clases involucradas en la jerarquía, comenzando desde la clase base más alta y descendiendo hasta la subclase específica. En el caso de crear un `Artillero`, primero se ejecuta y finaliza el constructor de `Soldado` para inicializar el estado base, y a continuación se ejecuta el cuerpo del constructor de `Artillero`.

La palabra reservada `super` actúa de manera análoga a `this`, pero referenciando a la superclase inmediata. Cuando se emplea como una llamada a función (`super(...)`) dentro de un constructor, sirve para invocar explícitamente a un constructor específico de la clase padre, pasándole los argumentos necesarios para que inicialice su parte del objeto.

Si la clase base carece de un constructor por defecto (sin parámetros) visible, es **estrictamente obligatorio** llamar a `super(...)` con los argumentos correspondientes. Además, esta llamada debe ser ineludiblemente la primera instrucción dentro del constructor de la subclase. Si no se hace, el compilador de Java emitirá un error, ya que no sabría cómo inicializar el estado heredado.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta
Sí, los atributos privados de la superclase forman parte íntegra y física de la instancia de la subclase en la memoria (Heap). Al utilizar el operador `new` para un `Artillero`, el sistema reserva un bloque de memoria lo suficientemente grande para albergar tanto el atributo `cohetes` como el atributo `nombre` heredado de `Soldado`. Estructuralmente, el objeto está completo.

Sin embargo, que existan en memoria no implica que puedan ser manipulados directamente. Debido a las reglas de la ocultación de información, el atributo `nombre` está marcado como `private` en la clase `Soldado`. Esto significa que el código fuente escrito dentro de la clase `Artillero` no tiene permisos del compilador para acceder a `this.nombre`. 

Para que el `Artillero` pueda interactuar con su propio nombre (a pesar de que resida dentro de su propio bloque de memoria), debe acatar la interfaz pública de la superclase. La subclase se ve obligada a utilizar métodos heredados que sí sean públicos, delegando en la lógica que fue programada originalmente en la clase `Soldado`.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta
La compatibilidad a nivel de tipos es la piedra angular de la extensibilidad en arquitecturas orientadas a objetos. Implica que los sistemas diseñados para operar con referencias de una superclase pueden incorporar nuevos comportamientos y variantes en el futuro sin que el código estructurado previamente deba ser reescrito, modificado o recompilado. Este concepto materializa el Principio Abierto/Cerrado (Open/Closed Principle) de la ingeniería de software.

En C, añadir un nuevo tipo a una estructura heterogénea normalmente exigiría añadir un nuevo valor a una enumeración (tipo `union`) y modificar múltiples bloques `switch` a lo largo de todo el programa para contemplar el nuevo caso. Con la herencia, basta con crear la nueva subclase. Cualquier función o bucle que ya operase con la superclase `Soldado` procesará la nueva variante de forma automática y transparente.

```java
// Nueva extensión del sistema sin tocar el código existente
class Francotirador extends Soldado {
    public Francotirador(String nombre) {
        super(nombre);
    }
    
    public void apuntar() { System.out.println("Objetivo fijado."); }
}

public class MainExtendido {
    public static void main(String[] args) {
        Soldado[] peloton = new Soldado[4];
        peloton[0] = new Soldado("Recluta Patoso");
        peloton[1] = new Artillero("Rambo", 5);
        peloton[2] = new Zapador("MacGyver", 10);
        // Se introduce el nuevo tipo
        peloton[3] = new Francotirador("Vassili"); 

        // El bucle original funciona perfectamente, no requiere modificación
        for (int i = 0; i < peloton.length; i++) {
            peloton[i].saludar(); 
        }
    }
}
```

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta
Tener una referencia de un supertipo apuntando a un objeto de un subtipo es completamente lícito y, de hecho, es la base del diseño orientado a objetos. Este proceso se denomina **"upcasting"** (conversión hacia arriba) y se realiza de manera implícita y automática, ya que es una operación segura: el sistema tiene la certeza de que todo `Artillero` es un `Soldado`.

No obstante, a través de una referencia del supertipo, el compilador **no permite** invocar métodos específicos del subtipo. Si una variable está declarada como `Soldado s`, el compilador solo reconoce los métodos definidos en `Soldado`. Para ejecutar la función de disparar cohetes, se debe realizar un **"downcasting"** (conversión hacia abajo), forzando explícitamente a la referencia a ser tratada como su tipo específico real, de forma idéntica a un casting de punteros en C `(Artillero) s`.

Dado que el "downcasting" es una operación arriesgada que abortará el programa si se intenta convertir un `Zapador` en un `Artillero`, se utiliza el operador booleano `instanceof`. Este operador consulta en tiempo de ejecución la naturaleza real del objeto al que apunta la referencia en la memoria, permitiendo verificar su clase exacta antes de proceder a la conversión.

```java
public class MainCastings {
    public static void main(String[] args) {
        Soldado[] peloton = new Soldado[2];
        peloton[0] = new Zapador("MacGyver", 10);
        peloton[1] = new Artillero("Rambo", 5); // Upcasting automático

        for (int i = 0; i < peloton.length; i++) {
            // Se comprueba la identidad real en memoria
            if (peloton[i] instanceof Artillero) {
                // Downcasting explícito y seguro
                Artillero a = (Artillero) peloton[i]; 
                System.out.println("Es un artillero con " + a.getCohetes() + " cohetes.");
            }
        }
    }
}
```

## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta
El modificador de acceso **protegido** (`protected`) representa un punto intermedio entre la restricción absoluta de `private` y la exposición total de `public`. Cuando un miembro de una clase se declara como protegido, permanece oculto y denegado para el exterior general del programa, pero se otorga permiso de acceso directo a cualquier clase que herede de ella (las subclases), sin importar en qué parte del proyecto se encuentren.

En Java, se implementa simplemente sustituyendo la palabra `private` por `protected` en la declaración del atributo o método en la superclase. Es importante destacar que, debido al diseño del lenguaje Java, el modificador `protected` también otorga visibilidad a cualquier otra clase que resida en el mismo "paquete" (directorio), aunque no formen parte de la misma jerarquía de herencia.

```java
class Soldado {
    // Al ser protected, las subclases pueden acceder directamente a la variable
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }
}

class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public void ponerMina() { 
        // Uso directo del atributo heredado de la superclase
        System.out.println(this.nombre + " ha colocado una mina."); 
    }
}
```

## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta
En la teoría general de la orientación a objetos, la existencia de una clase base universal (una clase primigenia de la que descienden absolutamente todas las demás) es una decisión de diseño del lenguaje, no un dogma ineludible del paradigma. Unificar todos los objetos bajo un solo árbol jerárquico permite crear estructuras de datos genéricas que acepten cualquier elemento del sistema.

No ocurre en todos los lenguajes. Lenguajes con un enfoque de control más bajo nivel como C++ permiten diseñar jerarquías de herencia completamente aisladas e independientes. En C++, es perfectamente posible declarar una clase que no herede de ninguna otra, dando lugar a un ecosistema compuesto por múltiples "bosques" de clases desconectadas entre sí.

En el lenguaje Java, sí existe una clase base universal denominada `java.lang.Object`. El ecosistema Java impone que toda clase creada por un desarrollador herede de `Object`. Si al definir una clase no se especifica explícitamente la directiva `extends`, el compilador inyecta automáticamente un `extends Object`. Esto garantiza que absolutamente cualquier instancia en Java posea métodos fundamentales y unificados, como `toString()`, `equals()` o `hashCode()`.

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta
La **herencia múltiple** es una capacidad arquitectónica que permite que una clase derive directamente de dos o más superclases simultáneamente. En lugar de tener un único padre, la nueva clase combinaría en su interior los atributos de estado (variables) y el comportamiento (métodos) de múltiples linajes distintos (por ejemplo, modelar un `CocheAnfibio` que herede características tanto de la clase `Automovil` como de la clase `Barco`).

Esta capacidad, presente en lenguajes como C++ o Python, introduce complejidades severas durante la compilación. El conflicto más célebre es el "Problema del Diamante": si ambas superclases definen un método con la misma firma o un atributo con idéntico nombre, el compilador se enfrenta a una ambigüedad insalvable al determinar cuál de las dos versiones debe heredar o ejecutar la subclase.

Por motivos de seguridad y simplificación estructural, **Java no permite la herencia múltiple de clases**. La instrucción `extends` se limita estrictamente a una única superclase. Sin embargo, Java ofrece una solución alternativa a través del concepto de "Interfaces", permitiendo que una clase herede comportamiento o contratos de múltiples orígenes, pero restringiendo la herencia de estado (atributos) a un único padre.

## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente.

### Respuesta
Dado que las excepciones son clases normales que participan en la jerarquía estándar, la creación de una excepción a medida se consigue estableciendo una relación de herencia con la rama correspondiente. Para que la anomalía sea clasificada por el compilador como "no controlada" (evitando la imposición estricta de bloques try-catch o cláusulas throws), la clase debe declararse como extensión directa o indirecta de `RuntimeException`.

Al ser una clase convencional, la excepción puede beneficiarse del mecanismo de composición. Se declara un atributo de tipo `Usuario` dentro de la excepción personalizada. Cuando se produce el fallo, la lógica que lo desencadena instancia esta excepción suministrándole la referencia del usuario problemático, de forma que los manejadores superiores obtengan el contexto completo de la entidad afectada, en lugar de recibir únicamente un texto plano.

La sobrecarga de constructores posibilita el encadenamiento de excepciones. Se define un segundo constructor que admita un objeto de tipo `Throwable`, el cual será delegado a la clase padre a través de la instrucción `super()`, integrando el error de bajo nivel originario en la pila de trazas (*stack trace*) final de la excepción de negocio.

```java
// Supongamos que existe esta clase previamente
class Usuario { 
    public String getId() { return "USR-99"; } 
}

// Excepción personalizada y no controlada por heredar de RuntimeException
public class UsuarioNoEncontradoException extends RuntimeException {
    
    // Composición: la excepción encapsula al objeto causante
    private final Usuario usuarioImplicado;

    // Constructor básico con mensaje y objeto
    public UsuarioNoEncontradoException(String mensaje, Usuario u) {
        super(mensaje);
        this.usuarioImplicado = u;
    }

    // Sobrecarga de constructor para incluir la causa subyacente
    public UsuarioNoEncontradoException(String mensaje, Usuario u, Throwable causa) {
        super(mensaje, causa); // Se delega la causa a la clase base estándar
        this.usuarioImplicado = u;
    }

    // Método getter para obtener el contexto rico del error
    public Usuario getUsuarioImplicado() {
        return usuarioImplicado;
    }
}
```

## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta
La herencia establece un contrato vinculante y lógico extremadamente rígido ("es-un"). Si se utiliza esta herramienta arquitectónica exclusivamente con el propósito de adquirir las funciones de otra clase, se corre el riesgo de modelar una jerarquía carente de sentido semántico. Un ejemplo clásico de este antipatrón es hacer que una clase `Pila` (LIFO) herede de una clase `ListaDinamica` simplemente para aprovechar sus algoritmos de inserción. 

Al establecer dicha herencia, la `Pila` absorbe y expone toda la interfaz pública de la `ListaDinamica`. En consecuencia, el código cliente ahora posee permisos legales del compilador para insertar elementos en medio de la pila o extraer de su base, violando por completo los principios matemáticos y la invariante de una estructura LIFO. La herencia destruye las restricciones lógicas de la nueva clase.

Para la simple reutilización de rutinas ya programadas, la alternativa correcta es la composición. Si la `Pila` contiene un atributo privado de tipo `ListaDinamica`, puede invocar internamente sus métodos para facilitar su propia implementación de almacenamiento, pero sin exponer las funciones perjudiciales al exterior, manteniendo un control absoluto sobre qué operaciones son admisibles en su interfaz pública.

## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta
La composición se considera preferible en el diseño moderno de software porque ofrece un grado de flexibilidad y bajo acoplamiento significativamente mayor. Las relaciones de herencia son estáticas e inmutables; se definen estrictamente en el código fuente en tiempo de compilación. Un objeto instanciado jamás puede modificar quién es su superclase durante la ejecución del programa.

Por el contrario, la composición opera en tiempo de ejecución. Los objetos que conforman el comportamiento interno de una clase (las referencias almacenadas como atributos) pueden ser reasignados o sustituidos dinámicamente. Esto permite alterar profundamente las capacidades operativas de una entidad sobre la marcha mediante la técnica de inyección de dependencias, un concepto inalcanzable para la herencia estática.

Además, el abuso de la herencia genera con frecuencia jerarquías de clases excesivamente profundas, frágiles y de difícil mantenimiento (el llamado problema de la "clase base frágil"). Una modificación menor en un método de una superclase en la cima del árbol puede desencadenar efectos colaterales impredecibles en docenas de subclases remotas. La composición, al basarse en interacciones mediante interfaces, confina los cambios a estructuras más pequeñas y aisladas.

## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta
La encapsulación busca aislar a los componentes de un sistema de los entresijos de su implementación subyacente. Sin embargo, en una relación de herencia, la subclase queda íntimamente atada a los detalles constructivos internos de la superclase. Para que una subclase funcione correctamente (especialmente cuando sobrescribe o modifica métodos heredados), el desarrollador suele necesitar conocer exactamente cómo la clase padre gestiona el flujo de llamadas entre sus propias funciones internas.

Si la superclase sufre una refactorización interna (por ejemplo, dividiendo un método complejo en dos subrutinas privadas) sin alterar su interfaz pública visible, las subclases que dependían de la antigua secuencia interna podrían dejar de operar correctamente. La subclase ha depositado una dependencia implícita y peligrosa sobre código que no le pertenece, vulnerando la promesa de cajas negras de la encapsulación.

Adicionalmente, el uso extensivo del modificador de acceso `protected` perfora deliberadamente la barrera de seguridad de los atributos privados. Permite a entidades externas (las subclases) eludir la validación estructural provista por los métodos "setters" de la superclase, facilitando que el estado del objeto original se corrompa y sus invariantes de diseño queden comprometidas desde una ubicación remota en la jerarquía.

## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta
El modelado mediante herencia consolida los atributos comunes en una entidad jerárquica superior (`Persona`). En esta configuración estructural, tanto el `Estudiante` como el `Trabajador` afirman categóricamente la relación "es-una", asimilando las variables de identificación directamente como suyas propias, fusionándose en un solo bloque en la memoria dinámica.

Por su parte, el modelado mediante composición aborda la reutilización extrayendo los datos comunes a un contenedor independiente (`DatosPersonales`). Las entidades finales ya no descienden de un antepasado común en el árbol de herencia, sino que establecen una relación de "tiene-un". Cada entidad es responsable de almacenar y gestionar un objeto estructural externo que contiene la información, delegando las consultas a dicha pieza modular.

```java
// === ALTERNATIVA 1: MODELADO POR HERENCIA ===
class Persona {
    protected String dni;
    protected String nombre;
}

class EstudianteHeredado extends Persona {
    private String matricula;
    public EstudianteHeredado(String d, String n, String m) {
        this.dni = d; // Atributo heredado
        this.nombre = n; // Atributo heredado
        this.matricula = m;
    }
}

// === ALTERNATIVA 2: MODELADO POR COMPOSICIÓN ===
class DatosPersonales {
    private String dni;
    private String nombre;
    public DatosPersonales(String d, String n) {
        this.dni = d;
        this.nombre = n;
    }
}

class EstudianteCompuesto {
    private DatosPersonales datos; // Composición
    private String matricula;

    public EstudianteCompuesto(DatosPersonales dp, String m) {
        this.datos = dp; // Se inyecta la pieza
        this.matricula = m;
    }
}
```
