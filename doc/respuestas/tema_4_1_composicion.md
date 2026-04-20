# Tema 4.1. Composición

## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta
En el lenguaje C, la creación de tipos de datos complejos a partir de tipos más simples se realiza anidando bloques `struct`. Esta técnica de diseño permite modelar relaciones estructurales jerárquicas. En el ejemplo solicitado, se define primero la entidad menor, el `Punto`, y posteriormente se define la entidad mayor, la `Linea`, la cual contiene físicamente en su interior dos instancias de la estructura inferior.

Esta composición estructural en C significa que la memoria se reserva de forma contigua. Cuando se instancia un `struct Linea`, el compilador reserva espacio para almacenar de forma consecutiva los datos correspondientes a dos `struct Punto` completos (cuatro números de tipo `double` en total). Es una relación de posesión estricta a nivel de gestión de memoria de la máquina.

```c
#include <stdio.h>
#include <math.h>

// Definición de la estructura básica
struct Punto {
    double x;
    double y;
};

// Composición: Una Linea "tiene-dos" Puntos
struct Linea {
    struct Punto origen;
    struct Punto destino;
};

// Función para calcular distancia entre dos puntos (pasados por valor para simplificar)
double calcular_distancia(struct Punto p1, struct Punto p2) {
    double dx = p1.x - p2.x;
    double dy = p1.y - p2.y;
    return sqrt(dx * dx + dy * dy);
}

// Función que opera sobre el compuesto utilizando la función del componente
double calcular_longitud_linea(struct Linea l) {
    // Delega el cálculo en la función de más bajo nivel
    return calcular_distancia(l.origen, l.destino);
}

int main() {
    struct Linea mi_linea = {{0.0, 0.0}, {3.0, 4.0}};
    printf("Longitud de la linea: %f\n", calcular_longitud_linea(mi_linea));
    return 0;
}
```

## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Respuesta


Al trasladar el concepto a la programación orientada a objetos en Java, la composición se establece mediante atributos de clase. La clase `Linea` declarará dos atributos del tipo `Punto`. A diferencia de C, donde los datos se alojaban de forma contigua, en Java la clase `Linea` almacena únicamente dos **referencias** (similares a punteros) a objetos `Punto` independientes que residen en la memoria dinámica (Heap).

Para superar las limitaciones de robustez de las estructuras públicas de C, se aplican los principios de encapsulación. Todos los atributos de estado (coordenadas en `Punto` y los extremos en `Linea`) se declaran como `private`. Al proporcionar constructores para inicializar los valores y omitir deliberadamente la implementación de métodos "setters", se impone un diseño inmutable. Una vez construidas, las instancias garantizan que sus datos no podrán ser alterados durante el resto de la ejecución.

```java
// Clase componente inmutable
class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double distanciaA(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

// Clase compuesta inmutable
class Linea {
    private final Punto origen;
    private final Punto destino;

    public Linea(Punto origen, Punto destino) {
        this.origen = origen;
        this.destino = destino;
    }

    public double calcularLongitud() {
        // La Línea delega la operación en sus componentes
        return this.origen.distanciaA(this.destino);
    }
}
```

## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta
En el diseño de software orientado a objetos, la **multiplicidad** (o cardinalidad) es un concepto estadístico prestado del modelado de bases de datos que define cuantitativamente los límites de una asociación entre clases. Establece cuántas instancias de una clase particular pueden estar vinculadas a una única instancia de otra clase en un momento dado de la ejecución del programa.

Esta restricción numérica se evalúa siempre en ambas direcciones para comprender completamente la naturaleza del vínculo estructural. Se suele expresar utilizando un formato de rangos (como `1..*` para "uno a muchos", `0..1` para "cero o uno", o valores fijos como `2`). Sirve como una especificación directa para programar validaciones lógicas e invariantes dentro del código.

Analizando el ejemplo anterior, la multiplicidad desde la perspectiva de `Linea` hacia `Punto` es estrictamente **2**. Una instancia válida de `Linea` requiere y contiene exactamente dos instancias de `Punto` (un origen y un destino) para existir matemáticamente. Evaluando la dirección opuesta, de `Punto` a `Linea`, la multiplicidad es **0..*** (cero a muchos). Un objeto `Punto` instanciado en memoria puede existir de forma independiente sin pertenecer a ninguna línea, puede formar parte de una sola línea, o podría ser referenciado simultáneamente como vértice o extremo por múltiples objetos `Linea` distintos.

## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta
La distinción entre estas dos modalidades radica fundamentalmente en quién detenta la responsabilidad sobre la existencia de los objetos y cómo se gestiona su destrucción. En la **composición fuerte** (referida estrictamente como **"composición"** en el vocabulario técnico de diseño UML), existe una dependencia existencial inquebrantable. El objeto contenedor es el propietario absoluto de sus partes.

La consecuencia directa sobre el ciclo de vida es que las partes nacen y mueren con el contenedor. Si el sistema destruye o descarta el objeto principal, todos sus objetos internos dependientes deben ser purgados también, ya que carecen de sentido o utilidad arquitectónica fuera de dicho contexto global (por ejemplo, las habitaciones pertenecen en exclusiva al edificio que las alberga).

En contraste, la **composición débil** (formalmente conocida como **"agregación"** o simplemente **"asociación"**) describe una relación colaborativa temporal o no exclusiva. El contenedor ensambla referencias a objetos independientes que han sido creados en otra parte del sistema. Si el contenedor es destruido, las partes mantienen su ciclo de vida y continúan existiendo, ya que pueden tener valor propio o estar participando simultáneamente en otras agrupaciones (por ejemplo, los profesores de un departamento universitario siguen siendo profesores aunque el departamento se disuelva).

## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta
En estos escenarios temporales y fugaces, nos referimos a una relación de **dependencia** (conocida también como relación "usa-un" o *uses-a* en la literatura anglosajona), la cual es arquitectónicamente distinta y mucho más tenue que una composición o agregación.

Para que exista una composición (en cualquiera de sus niveles de fuerza), un objeto debe almacenar a otro como parte integral y duradera de su **estado interno**. Esto implica imperativamente declarar variables a nivel de clase (los atributos privados definidos bajo el encapsulamiento). La relación estructural perdura en el tiempo mientras el contenedor mantenga su estado en memoria.

Cuando una clase únicamente interactúa con objetos de otra instanciándolos dentro de una función, recibiéndolos como parámetros o devolviéndolos tras un cálculo, el vínculo se extingue en el instante mismo en que finaliza la ejecución de dicha subrutina y se destruye el entorno local de la pila (Stack). La clase actual "depende" de la existencia de la otra para completar una tarea específica y compilar correctamente, pero no la incorpora estructuralmente como un fragmento de sí misma a largo plazo.

## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta
La implementación técnica de estas semánticas de diseño se refleja primariamente en los constructores. Para emular una **composición fuerte**, la clase constructora debe aislar la creación de sus componentes del exterior. El método llamador aporta únicamente datos primitivos, y es la propia `Linea` quien ejecuta internamente los comandos `new` para dar a luz a sus puntos. El sistema asume que nadie más tiene referencias a esos puntos recién nacidos, ligando sus ciclos de vida.

```java
// Variante A: Composición Fuerte (Creación interna)
class LineaFuerte {
    private final Punto origen;
    private final Punto destino;

    // Recibe datos, pero crea y posee sus propios componentes
    public LineaFuerte(double x1, double y1, double x2, double y2) {
        this.origen = new Punto(x1, y1);
        this.destino = new Punto(x2, y2);
    }
}
```

Para modelar una **composición débil** (o agregación), la clase asume un rol receptor. Las instancias de `Punto` se fabrican de manera autónoma en alguna otra porción del programa, asegurando su existencia independiente. La `Linea` simplemente recibe las referencias a través de sus parámetros y las archiva en sus atributos. Si la línea desaparece, el código externo que fabricó los puntos originales todavía mantiene acceso a ellos, preservando sus ciclos de vida.

```java
// Variante B: Composición Débil / Agregación (Inyección externa)
class LineaDebil {
    private final Punto origen;
    private final Punto destino;

    // Recibe objetos preexistentes y los archiva
    public LineaDebil(Punto origenExterno, Punto destinoExterno) {
        this.origen = origenExterno;
        this.destino = destinoExterno;
    }
}
```

## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta
En entornos de memoria gestionada manual y estrictamente como C/C++, la responsabilidad de la composición fuerte impone al programador redactar destructores explícitos que invoquen comandos `free()` o `delete` sobre las partes internas, previniendo así la fuga de recursos hacia el sistema operativo cuando el contenedor perece.

En el ecosistema virtual de Java, este paradigma difiere sustancialmente gracias a la presencia del **Recolector de Basura** (*Garbage Collector*). No es necesario ni posible forzar la destrucción afirmativa de las partes internas de manera manual. El proceso de liquidación ocurre de forma pasiva y algorítmica, delegando la responsabilidad de la memoria a un hilo de ejecución subyacente del sistema.

Cuando el programa deja de interactuar con una instancia particular de `LineaFuerte` y pierde el rastro de la última referencia existente hacia dicho contenedor, la Máquina Virtual lo clasifica como "inalcanzable". Consecuentemente, como los únicos enlaces hacia los objetos `Punto` internos residían exclusivamente dentro de esa línea aislada (una prerrogativa de la composición fuerte), los puntos también se vuelven inalcanzables en cadena. En su próximo ciclo de patrullaje, el recolector de basura los localizará y barrerá toda esa ramificación de la memoria simultáneamente, asegurando el acoplamiento del ciclo de vida sin intervención del desarrollador.

## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta
Este diseño ejemplifica una arquitectura con dos ejes de composición simultáneos sobre la misma entidad: una relación de agregación masiva (la plantilla) y una relación especializada (la dirección). La clase `Departamento` actúa como gestora, protegiendo ferozmente su coherencia interna. Al estar basada en arreglos estáticos nativos (al igual que en C), demanda una gestión manual meticulosa para desplazar elementos en bloque y tapar los huecos provocados por las eliminaciones de personal.

La salvaguarda de la invariante (el director debe residir inevitablemente entre las filas de los docentes habituales) dictamina que las operaciones estructurales deben incorporar validaciones lógicas antes de consumarse. Si un intento de inserción colapsa la capacidad predefinida, o si una directriz pretende expulsar a un miembro que ostenta cargos de dirección, la clase aborta la maniobra de forma hostil emitiendo excepciones no controladas.

```java
class Profesor {
    private String nombre;
    public Profesor(String n) { this.nombre = n; }
    public String getNombre() { return nombre; }
}

class Departamento {
    private Profesor[] profesores;
    private Profesor director;
    private int numProfesores;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) {
            throw new IllegalArgumentException("El departamento debe nacer con un director");
        }
        this.profesores = new Profesor[50];
        this.profesores[0] = directorInicial;
        this.director = directorInicial;
        this.numProfesores = 1;
    }

    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("Profesor inválido");
        if (numProfesores >= 50) throw new IllegalStateException("Capacidad máxima alcanzada");
        profesores[numProfesores] = p;
        numProfesores++;
    }

    public void eliminarProfesorPorPosicion(int pos) {
        if (pos < 0 || pos >= numProfesores) throw new IndexOutOfBoundsException("Posición inválida");
        
        // Proteccion de invariante: No eliminar al director.
        // Ojo, comparamos por IDENTIDAD (==), buscando el mismo objeto exacto en memoria.
        if (profesores[pos] == director) {
            throw new IllegalStateException("No se puede eliminar al director. Cámbielo primero.");
        }

        // Algoritmo clásico en C para compactar el array
        for (int i = pos; i < numProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[numProfesores - 1] = null; // Limpiar última referencia para Garbage Collector
        numProfesores--;
    }

    public void nombrarNuevoDirectorPorPosicion(int pos) {
        if (pos < 0 || pos >= numProfesores) throw new IndexOutOfBoundsException("Posición inválida");
        // Aseguramos que el nuevo director es un profesor ya existente en la lista
        this.director = profesores[pos];
    }

    // Interfaz pública para consultar sin exponer el array
    public int getNumeroProfesores() { return numProfesores; }
    
    public Profesor getProfesor(int pos) {
        if (pos < 0 || pos >= numProfesores) throw new IndexOutOfBoundsException("Posición inválida");
        return profesores[pos];
    }
}
```

## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta
El ecosistema moderno de Java facilita estructuras de datos dinámicas contenidas en el `Java Collections Framework`. Sustituir el vector estático por una `List` (típicamente instanciada mediante su implementación `ArrayList`) automatiza la tediosa administración del estado subyacente. Al realizar este cambio, el desarrollador se ahorra la totalidad del algoritmo de corrimiento y compactación de memoria asociado a la eliminación de componentes, así como el seguimiento manual de la longitud efectiva de la colección mediante un contador entero segregado.

Si se optara por añadir una función `public List<Profesor> getTodosLosProfesores()` que retorne llanamente el atributo interno (`return this.profesores;`), se desencadenaría una ruptura crítica de la encapsulación, conocida como fuga de referencias (*Reference Escape*). El método llamador obtendría el "mando a distancia" originario de la lista estructural del departamento, facultándolo para borrar o insertar elementos directamente, ignorando olímpicamente las validaciones y rompiendo la invariante central.

La contingencia para mitigar este desajuste arquitectónico, proveyendo la información sin comprometer la integridad, demanda retornar un mecanismo pasivo. Se resuelve instanciando un objeto envoltura de protección, como `Collections.unmodifiableList(this.profesores)`, que retorna una proyección de solo lectura, o instanciando un `ArrayList` completamente nuevo que albergue réplicas de las referencias originales antes de enviarlo.

```java
import java.util.ArrayList;
import java.util.List;

class DepartamentoConLista {
    private List<Profesor> profesores;
    private Profesor director;

    public DepartamentoConLista(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException("Requiere director");
        // Inicialización dinámica, sin límites arbitrarios a priori
        this.profesores = new ArrayList<>();
        this.profesores.add(directorInicial);
        this.director = directorInicial;
    }

    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException();
        // La lista gestiona su propio tamaño automáticamente
        profesores.add(p);
    }

    public void eliminarProfesorPorPosicion(int pos) {
        if (pos < 0 || pos >= profesores.size()) throw new IndexOutOfBoundsException();
        if (profesores.get(pos) == director) throw new IllegalStateException("No eliminar director");
        
        // Compactación y corrimiento internos resueltos por la API de List
        profesores.remove(pos);
    }

    public void nombrarNuevoDirectorPorPosicion(int pos) {
        if (pos < 0 || pos >= profesores.size()) throw new IndexOutOfBoundsException();
        this.director = profesores.get(pos);
    }

    public int getNumeroProfesores() { return profesores.size(); }
    
    public Profesor getProfesor(int pos) {
        if (pos < 0 || pos >= profesores.size()) throw new IndexOutOfBoundsException();
        return profesores.get(pos);
    }
}
```

## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta
La arquitectura de una composición recursiva se formaliza cuando los atributos internos de una clase albergan referencias cuyo tipo de dato se corresponde idénticamente con el de la clase contenedora. Esta autorreferencialidad estructural, familiar para aquellos que han manejado listas enlazadas en lenguaje C mediante el uso sistemático de `struct Nodo *siguiente`, propicia el diseño de modelos jerárquicos o anidados de profundidad ilimitada.

En un diagrama familiar concebido de forma inmutable, la entidad debe proveer la referencia de su ancestro desde el momento germinal de la instanciación. El caso base de dicha recursión arquitectónica—el ancestro originario—se representa mediante la inserción deliberada del valor nulo (`null`), señalizando formalmente el estrato final de la ramificación hacia el pasado.

Adicionalmente al parentesco, los ejemplos ortodoxos de ensamblaje recursivo abarcan configuraciones informáticas como los sistemas de ficheros, donde un `Directorio` está compuesto internamente por colecciones de otros objetos de tipo `Directorio`; la representación de expresiones algebraicas mediante árboles abstractos; o el modelado organizativo de recursos humanos donde una clase `Empleado` incorpora un campo para su supervisor directo, que lógicamente, recae también bajo el tipo `Empleado`.

```java
// Clase inmutable con asociación recursiva
class Persona {
    private final String nombre;
    // La clase se contiene a sí misma
    private final Persona madre;

    public Persona(String nombre, Persona madre) {
        this.nombre = nombre;
        this.madre = madre;
    }

    public String getNombre() {
        return nombre;
    }

    // Retorna la instancia que sirve de ancestro
    public Persona getMadre() {
        return madre;
    }
}

public class ArbolGenealogico {
    public static void main(String[] args) {
        // La abuela actúa como nodo raíz de la recursión (madre = null)
        Persona abuela = new Persona("Carmen", null);
        
        // Las instancias subsecuentes componen dependencias encadenadas
        Persona hija = new Persona("María", abuela);
        Persona nieto = new Persona("Carlos", hija);

        System.out.println("Nieto: " + nieto.getNombre());
        System.out.println("Su madre es: " + nieto.getMadre().getNombre());
        System.out.println("Su abuela es: " + nieto.getMadre().getMadre().getNombre());
    }
}
```

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta
En la teoría del modelado arquitectónico, una relación bidireccional consolida un enlace recíproco y simétrico entre entidades. No sólo la clase primaria (el `Departamento`) conserva referencias y conocimiento operacional sobre sus elementos subordinados (los `Profesores`), sino que, a la inversa, la clase secundaria expone atributos que apuntan retroactivamente hacia el contenedor global al que pertenece, estableciendo un flujo de información circular en doble sentido.

Para instaurar este paradigma en el caso propuesto, se requeriría una reestructuración dual. De forma primordial, la definición de la clase `Profesor` debería mutar para albergar una nueva variable privada denominada `departamentoDeAdscripcion` de tipo `Departamento`. Este puente retroactivo posibilitaría a cualquier miembro de la plantilla indagar, mediante una subrutina tipo `getDepartamento()`, en qué unidad organizativa se encuentra prestando servicios en un momento temporal concreto.

De forma complementaria, el ensamblaje o inyección de dependencias vería su nivel de complejidad drásticamente incrementado. Para soslayar un colapso en la integridad de la información, si el `Departamento` registra al `Profesor` utilizando su función de inserción interna, el código debe velar infaliblemente porque, en esa misma maniobra atómica, el sistema actualice también el atributo adscrito de la instancia docente para apuntar al objeto `this`. La dificultad reside en gobernar dos entidades separadas de modo que ninguna mantenga representaciones divergentes del sistema.
