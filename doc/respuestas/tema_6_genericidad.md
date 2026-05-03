<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta
En lenguajes como Java, es posible aprovechar la jerarquía de clases donde todos los objetos heredan de Object para crear estructuras genéricas de manera primitiva. En el caso de C, al carecer de orientación a objetos, se recurre al uso de punteros genéricos void*, los cuales tienen la capacidad de apuntar a cualquier dirección de memoria sin importar el tipo de dato que allí se encuentre.

Una estructura de datos básica, como un contenedor, puede implementarse utilizando un array basado en estos tipos universales. De esta forma, un único array es capaz de almacenar simultáneamente referencias a diferentes tipos de datos. A continuación, se presenta un ejemplo en Java donde se define una clase para almacenar múltiples elementos valiéndose de un array de Object.

```java
public class ContenedorUniversal {
    private Object[] elementos;
    private int contador;

    public ContenedorUniversal(int capacidad) {
        elementos = new Object[capacidad];
        contador = 0;
    }

    public void add(Object elemento) {
        if (contador < elementos.length) {
            elementos[contador] = elemento;
            contador++;
        }
    }

    public Object get(int indice) {
        return elementos[indice];
    }
}

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta
La programación genérica es un paradigma que permite diseñar algoritmos y estructuras de datos de manera independiente a los tipos concretos sobre los que van a operar. Su objetivo principal es escribir el código lógico una única vez y permitir que este sea reutilizado con distintos tipos de datos (enteros, cadenas, objetos complejos), especificando el tipo exacto en el momento de la instanciación o invocación, sin sacrificar la seguridad en la validación de tipos.

El ejemplo elaborado con Object (o void* en C) no se considera un modelo de programación genérica pura. Más bien, es una técnica basada en el polimorfismo de inclusión (en Java) o en la manipulación cruda de memoria (en C). Aunque efectivamente permite que una misma estructura aloje cualquier tipo de dato, adolece de la característica más crítica de los mecanismos de genericidad modernos: la capacidad de parametrizar formalmente los tipos para que el compilador pueda realizar validaciones estrictas y automáticas.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta
El principal problema de emplear Object o void* es la pérdida absoluta de información sobre el tipo de dato real que se está manipulando. Cuando se introduce un elemento en la estructura, el compilador olvida su tipo original y solo reconoce que se trata de un "objeto genérico" o una "dirección de memoria". Por consiguiente, el compilador es incapaz de detectar si se introducen tipos incompatibles de forma accidental, imposibilitando el chequeo estático de tipos.

Al extraer los elementos de dicha estructura, es estrictamente necesario aplicar una conversión explícita de tipos (conocida como downcasting en Java o casting de punteros en C) para recuperar la funcionalidad del dato original. Si se comete un error y se asume un tipo incorrecto durante esta conversión, el programa compilará sin advertencias, pero fallará gravemente en tiempo de ejecución. En Java, esto se manifestará como una excepción de tipo ClassCastException, mientras que en C provocará comportamientos indefinidos o violaciones de segmento.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta
Los parámetros de tipo son identificadores simbólicos (comúnmente representados por letras mayúsculas como <T>, <E> o <K>) que actúan como marcadores de posición o "comodines" dentro de la definición de una clase, interfaz o método. Funcionan de manera análoga a los parámetros tradicionales de las funciones, pero en lugar de recibir valores concretos (como un número o una cadena), reciben tipos de datos (como Integer, String o una clase definida por el usuario).

Al utilizar parámetros de tipo, se establece un contrato formal que indica que una estructura o algoritmo trabajará exclusivamente con el tipo que se le especifique posteriormente. Esto permite delegar la decisión sobre el tipo concreto al momento en que el desarrollador instancia la clase o invoca el método, garantizando que el compilador tenga toda la información necesaria para realizar un análisis sintáctico y de tipos riguroso antes de la ejecución.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta
Tanto en Java como en C++ existen mecanismos nativos para implementar colecciones fuertemente tipadas mediante parámetros de tipo. En Java, esto se logra empleando Generics en interfaces y clases como List y ArrayList. Al instanciar la colección, se especifica entre los símbolos de diamante < > el tipo exacto que esta debe contener, impidiendo la inserción de cualquier otro objeto y evitando conversiones manuales al extraerlos.

En C++, el paradigma se implementa a través de las templates (plantillas), siendo std::vector de la biblioteca estándar (STL) el equivalente más directo. Al igual que en Java, el tipo se define en la declaración. A pesar de que C++ permite un enfoque más cercano a la programación estructurada y gestión manual de memoria, el uso de plantillas en la biblioteca estándar ofrece garantías de seguridad de tipos similares durante la compilación.

A continuación, se exponen ambos ejemplos, demostrando cómo se instancian, rellenan y recorren de forma segura sin requerir casting:

```java
// Ejemplo en Java
import java.util.ArrayList;
import java.util.List;

public class EjemploGenerics {
    public static void main(String[] args) {
        // Instanciación segura: solo admite String
        List<String> listaNombres = new ArrayList<>();
        listaNombres.add("Ada");
        listaNombres.add("Alan");
        
        // El compilador sabe que son String, no hace falta cast
        for (String nombre : listaNombres) {
            System.out.println("Longitud de " + nombre + ": " + nombre.length());
        }
    }
}

```c
// Ejemplo en C++
#include <iostream>
#include <vector>
#include <string>

int main() {
    // Instanciación segura: solo admite std::string
    std::vector<std::string> listaNombres;
    listaNombres.push_back("Ada");
    listaNombres.push_back("Alan");
    
    // El compilador sabe que son std::string
    for (const std::string& nombre : listaNombres) {
        std::cout << "Longitud de " << nombre << ": " << nombre.length() << std::endl;
    }
    return 0;
}

## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta
Los compiladores de Java y C++ abordan la resolución de los parámetros de tipo mediante estrategias completamente distintas. En C++, se utiliza la "instanciación de plantillas" (template instantiation). Cuando el compilador detecta que se utiliza una plantilla con un tipo específico (por ejemplo, <std::string>), genera y compila una copia completamente nueva del código fuente de la clase o función reemplazando internamente el parámetro por el tipo concreto. Si se usa la misma plantilla con tres tipos distintos, existirán tres versiones de código compilado en el ejecutable final, lo que optimiza la ejecución pero aumenta el tamaño del archivo binario.

Por el contrario, Java implementa un mecanismo denominado "borrado de tipos" (type erasure). Para garantizar la compatibilidad hacia atrás con versiones antiguas de Java que no poseían genéricos, el compilador de Java utiliza la información de los genéricos únicamente durante la fase de compilación para validar la seguridad de los tipos. Una vez superados los chequeos, el compilador elimina (borra) todos los parámetros de tipo del código generado (el bytecode).

Como resultado de este borrado, los tipos parametrizados <T> se sustituyen en el código compilado por Object (o por el límite superior si hay restricciones). Además, el compilador inserta de forma transparente e invisible los castings necesarios cada vez que se extrae un elemento. Por tanto, en Java no se generan múltiples copias de la clase: solo existe una única versión compilada que manipula internamente objetos genéricos, siendo la máquina virtual ignorante de los genéricos originales durante la ejecución.

## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta
Para definir una clase que pueda contener dos tipos de datos simultáneamente de manera genérica, es posible declarar múltiples parámetros de tipo en la cabecera de la clase, separándolos por comas, como <T, U>. Esto permite que, en el momento de la instanciación, se puedan elegir tipos iguales o distintos para cada atributo interno, manteniendo en todo momento la comprobación de tipos por parte del compilador.

A continuación, se define la clase Par y se ilustra su utilidad práctica aplicándola como valor de retorno en un método matemático. Esto solventa la limitación clásica de lenguajes como Java y C, los cuales, por defecto, solo permiten retornar un único valor por función.

```java
public class Par<T, U> {
    private final T primero;
    private final U segundo;

    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public T getPrimero() { return primero; }
    public U getSegundo() { return segundo; }
}

public class Estadistica {
    // Retorna simultáneamente la media y la desviación típica
    public static Par<Double, Double> calcularEstadisticas(double[] datos) {
        double suma = 0;
        for (double d : datos) suma += d;
        double media = suma / datos.length;

        double sumaVarianzas = 0;
        for (double d : datos) sumaVarianzas += Math.pow(d - media, 2);
        double desviacion = Math.sqrt(sumaVarianzas / datos.length);

        return new Par<>(media, desviacion);
    }

    public static void main(String[] args) {
        double[] muestra = {2.5, 4.1, 5.8, 3.2};
        Par<Double, Double> resultados = calcularEstadisticas(muestra);
        
        System.out.println("Media: " + resultados.getPrimero());
        System.out.println("Desviación: " + resultados.getSegundo());
    }
}


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta
La genericidad a nivel de método permite que funciones específicas declaren sus propios parámetros de tipo, independientes de los de la clase en la que residen. Esto se logra colocando el parámetro de tipo (por ejemplo, <T>) justo antes del tipo de retorno en la firma del método. Esta técnica resulta idónea para funciones utilitarias que operan con argumentos de manera genérica.

Si se implementa el método utilizando Object tradicional, se permite la entrada de cualquier combinación de clases, lo que destruye la restricción de que ambos argumentos deban ser del mismo tipo. Además, el valor devuelto será de tipo Object, forzando a quien invoca el método a realizar un downcasting manual, asumiendo riesgos de ejecución. Por el contrario, al usar genéricos, se exige a nivel de compilación que los argumentos compartan un tipo común <T> y el retorno se garantiza de dicho tipo exacto, eliminando la necesidad de conversiones posteriores.

```java
import java.util.Random;

public class Utilidades {
    private static final Random random = new Random();

    // Enfoque clásico con Object: requiere cast y permite mezclar tipos.
    public static Object seleccionaUnoObject(Object a, Object b) {
        return random.nextBoolean() ? a : b;
    }

    // Enfoque con Métodos Genéricos: fuerza mismo tipo y no requiere cast.
    public static <T> T seleccionaUnoGenerico(T a, T b) {
        return random.nextBoolean() ? a : b;
    }

    public static void main(String[] args) {
        // Uso con Object:
        // 1. Permite mezclar tipos (peligroso o indeseado).
        // 2. Exige downcasting al recibir el resultado.
        String resultadoObj = (String) seleccionaUnoObject("Hola", "Adios"); 
        
        // Uso con Genéricos:
        // 1. Fuerza que ambos sean String (si pones un Integer daría error de compilación).
        // 2. Devuelve String directamente.
        String resultadoGen = seleccionaUnoGenerico("Hola", "Adios"); 
    }
}


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta
Efectivamente, es posible aplicar límites o restricciones (bounds) a los parámetros de tipo en Java mediante la sintaxis <T extends ClaseBase>. Esta construcción indica al compilador que el tipo genérico proporcionado debe ser forzosamente la clase especificada o una subclase de esta. Esta característica permite invocar de forma segura los métodos definidos en la clase límite directamente sobre las variables de tipo <T>, operando sobre ellas como si fueran de ese tipo base concreto.

A continuación, se presentan dos implementaciones para modelar un punto en el espacio. La primera aprovecha el polimorfismo convencional mediante la clase abstracta Number. La segunda emplea genéricos con restricción superior. En cuanto al proceso de "type erasure" de la segunda solución, dado que el parámetro de tipo tiene un límite, el compilador reemplazará todas las ocurrencias de <T> por el límite superior declarado, es decir, por Number.

```java
// Solución 1: Sin Genéricos (usando polimorfismo clásico)
public class PuntoSinGenerics {
    private final Number x, y;

    public PuntoSinGenerics(Number x, Number y) {
        this.x = x; this.y = y;
    }

    public Number getX() { return x; }
    public Number getY() { return y; }

    public double calcularDistanciaA(PuntoSinGenerics p) {
        double difX = this.x.doubleValue() - p.x.doubleValue();
        double difY = this.y.doubleValue() - p.y.doubleValue();
        return Math.sqrt(difX * difX + difY * difY);
    }
}

// Solución 2: Con Genéricos restringidos (Bounded Type Parameters)
public class PuntoConGenerics<T extends Number> {
    private final T x, y;

    public PuntoConGenerics(T x, T y) {
        this.x = x; this.y = y;
    }

    public T getX() { return x; }
    public T getY() { return y; }

    public double calcularDistanciaA(PuntoConGenerics<?> p) {
        double difX = this.x.doubleValue() - p.x.doubleValue();
        double difY = this.y.doubleValue() - p.y.doubleValue();
        return Math.sqrt(difX * difX + difY * difY);
    }
}

## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta
Las implicaciones respecto a la seguridad y precisión de tipos varían notablemente entre ambas soluciones. En el enfoque sin genéricos, dado que los atributos se declaran explícitamente como Number, es perfectamente posible instanciar un punto donde la coordenada X sea un Integer y la coordenada Y sea un Double. Además, cuando se invoca el método getX(), este retorna una referencia de tipo genérico Number. Si el código que recibe este valor necesita invocar métodos específicos de Integer o Double, se verá obligado a aplicar un downcasting.

Por otro lado, la solución implementada con genéricos <T extends Number> ofrece una cohesión de tipos mucho más estricta. Al instanciar PuntoConGenerics<Integer>, el compilador exige que ambas coordenadas sean forzosamente enteros; un intento de mezclar un Integer con un Double resultará en un error de compilación. Consecuentemente, el método getX() adaptará su firma para retornar exactamente el tipo instanciado (en este caso, Integer), eliminando por completo la necesidad de conversiones posteriores y garantizando una homogeneidad total en la estructura.


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta
Para resolver el problema del chequeo dinámico de tipos y evitar el uso de instanceof al comparar objetos dentro de una jerarquía de clases, se puede aplicar un patrón de diseño avanzado conocido como "Patrón de Plantilla Curiosamente Recurrente" o CRTP (por sus siglas en inglés, Curiously Recurring Template Pattern), adaptado al sistema de genéricos de Java.

La técnica consiste en parametrizar la interfaz base consigo misma. Se define la interfaz Punto con un parámetro de tipo <T extends Punto<T>>. Esto establece un contrato semántico que obliga a las clases que implementen la interfaz a definir el tipo del argumento del método distanciaA como su propio tipo exacto. De este modo, el compilador puede rechazar directamente en tiempo de compilación cualquier intento de calcular la distancia entre un Punto2D y un Punto3D.

```java
// La interfaz requiere que el tipo genérico sea un subtipo de sí misma
public interface Punto<T extends Punto<T>> { 
    public double distanciaA(T p); 
} 

// Punto2D se pasa a sí mismo como argumento de tipo
public class Punto2D implements Punto<Punto2D> { 
    private final double x, y; 
    
    public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto2D p) { 
        // Ya no hace falta instanceof ni downcasting. 
        // 'p' está garantizado por el compilador de ser Punto2D.
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2)); 
    } 
} 

// Punto3D hace lo propio
public class Punto3D implements Punto<Punto3D> { 
    private final double x, y, z;
    
    public Punto3D(double x, double y, double z) {
        this.x = x; this.y = y; this.z = z;
    }

    @Override 
    public double distanciaA(Punto3D p) { 
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2) + Math.pow(z - p.z, 2)); 
    } 
}


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta
La respuesta varía fundamentalmente por el diseño del lenguaje. Un String[] sí es considerado un subtipo de un Object[], lo que permite asignar un array de cadenas a una referencia de array de objetos. Sin embargo, un List<String> no es subtipo de un List<Object>, a pesar de la relación jerárquica entre sus tipos contenidos. Esta decisión en el diseño de las listas genéricas se tomó para evitar brechas graves en la seguridad de tipos, solventando un fallo histórico presente en los arrays.

El problema que surge en tiempo de ejecución con los arrays radica en que, al permitir tratar a String[] como Object[], se abre la puerta a que el programa inserte elementos de otros tipos (por ejemplo, un Integer) en el array, ya que la referencia es de Object. Esto compilará sin problemas, pero al ejecutarse lanzará una excepción ArrayStoreException, dado que el array en memoria sigue siendo exclusivo para String. Los genéricos corrigen esto denegando dicha asignación desde la etapa de compilación.

A partir de este comportamiento, se definen tres propiedades fundamentales respecto a los tipos parametrizados y el polimorfismo. Un tipo es covariante si respeta la misma jerarquía que sus parámetros de tipo (como sucede con los arrays). Es contravariante si invierte la relación de herencia de sus parámetros. Finalmente, un tipo es invariante si carece por completo de relación jerárquica entre las distintas instanciaciones de sus genéricos, sin importar la relación de los tipos base subyacentes. Por defecto, los genéricos en Java son invariantes.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta
Un wildcard o comodín, representado por el símbolo de interrogación ?, es una herramienta en la sintaxis de Java que denota un "tipo desconocido". Se emplea fundamentalmente para dotar a las referencias genéricas de flexibilidad jerárquica (covarianza y contravarianza), mitigando la limitación impuesta por la invariancia estricta de las colecciones, pero haciéndolo de un modo controlado y seguro por el compilador.

La sintaxis List<? extends T> establece una restricción superior, logrando covarianza. Significa que la lista puede contener objetos de tipo T o de cualquier subclase de T. Su caso de uso principal es para estructuras productoras (solo lectura): se puede estar seguro de que al leer, se obtendrá al menos un elemento compatible con T, pero el compilador prohibirá insertar nuevos elementos para no corromper la colección. En contraste, List<? super T> establece una restricción inferior, logrando contravarianza. Indica que la lista es de tipo T o de alguna superclase de T. Su utilidad reside en estructuras consumidoras (solo escritura): garantiza que sea seguro inyectar objetos de tipo T (o sus derivados) a la lista, pero no proporciona garantías seguras al extraerlos.

```java
import java.util.List;

public class EjemploWildcards {
    
    // (i) Uso de ? extends T (Covarianza / Solo Lectura)
    // Acepta List<Number>, List<Integer>, List<Double>...
    public static double sumar(List<? extends Number> lista) {
        double total = 0.0;
        for (Number num : lista) {
            total += num.doubleValue(); // Es seguro leer, sabemos que es al menos Number
        }
        // lista.add(10); // ERROR: El compilador prohíbe la inserción
        return total;
    }

    // (ii) Uso de ? super T (Contravarianza / Solo Escritura)
    // Acepta List<Integer>, List<Number>, List<Object>...
    public static void agregarEnteros(List<? super Integer> lista) {
        // Es seguro añadir enteros a una lista que soporta Integer o algo superior
        lista.add(10);
        lista.add(20);
        // Integer n = lista.get(0); // ERROR: No es seguro asumir que la extracción dará Integer
    }
}
```</Object></String></Object></String></T></Integer></T></T></T></T></T></T></T></T,></T></K></E></T>