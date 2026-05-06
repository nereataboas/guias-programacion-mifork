<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a una función es una variable que, en lugar de almacenar un valor de datos convencional (como un entero o un carácter), almacena la dirección de memoria donde residen las instrucciones ejecutables de una función. En lenguajes de programación procedurales como C o C++, el nombre de una función actúa como la dirección de memoria de su código. Por lo tanto, se puede declarar un puntero específico que apunte a esa firma (tipo de retorno y parámetros), lo que permite invocar la función de forma indirecta a través de la variable.

El uso de punteros a funciones resulta de gran utilidad para diseñar sistemas flexibles en lenguajes sin orientación a objetos. Permite pasar funciones como argumentos a otras funciones (conocido comúnmente como "callbacks"), crear arreglos de funciones para ejecutar lógicas basadas en índices, o implementar máquinas de estado y tablas de despacho dinámicas, logrando un comportamiento polimórfico sin necesidad de clases o herencia.

A continuación, se muestra un ejemplo en C. Dado que en C el manejo de cadenas es mediante punteros y la gestión de memoria requiere cuidado, la función modifica la cadena original en el sitio y devuelve el puntero a la misma cadena resultante.

```c
#include <stdio.h>
#include <ctype.h>

// Definición de la función
char* convertirAMayusculas(char* cadena) {
    int i = 0;
    while (cadena[i] != '\0') {
        cadena[i] = toupper((unsigned char)cadena[i]);
        i++;
    }
    return cadena;
}

int main() {
    char texto[] = "hola mundo";
    
    // Declaración del puntero a la función
    char* (*aMayusculas)(char*) = convertirAMayusculas;
    
    // Invocación mediante el puntero
    printf("Resultado: %s\n", aMayusculas(texto));
    
    return 0;
}

## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima (sin nombre identificador asociado) que se puede definir de manera concisa directamente en el lugar donde se necesita. Este tipo de funciones se utiliza habitualmente para crear pequeños bloques de código que se ejecutan una sola vez, o para ser pasadas como argumentos a otras funciones, simplificando la escritura de código y evitando la necesidad de declarar funciones formales para lógicas triviales o de un solo uso.

En lenguajes modernos, las expresiones lambda suelen tener una sintaxis muy reducida, basada en un operador de flecha que separa los parámetros de entrada del cuerpo de la función. Aunque la esencia es similar a la de un puntero a función por el hecho de poder referenciar código ejecutable mediante una variable, las lambdas se integran profundamente con el sistema de tipos y permiten capturar el contexto donde fueron creadas, un concepto que no existe en los punteros a funciones tradicionales.

A continuación, se presentan los ejemplos solicitados. Nótese cómo la variable aMayusculas no guarda el resultado de la ejecución, sino la definición de la lógica en sí misma, para ser invocada posteriormente.

```javascript
// Definición de la función lambda asignada a una variable
const aMayusculas = (cadena) => cadena.toUpperCase();

// Invocación
let texto = "hola mundo";
console.log("Resultado: " + aMayusculas(texto));

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        // Definición de la función lambda asignada a una variable funcional
        Function<String, String> aMayusculas = cadena -> cadena.toUpperCase();
        
        // Invocación mediante el método apply de la interfaz Function
        String texto = "hola mundo";
        System.out.println("Resultado: " + aMayusculas.apply(texto));
    }
}


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?


### Respuesta
El paradigma funcional es un estilo de programación declarativo donde la computación se trata como la evaluación de funciones matemáticas, evitando el cambio de estado y la mutación de datos. En lugar de ejecutar secuencias de comandos que alteran variables en la memoria (como ocurre en el paradigma imperativo tradicional), se enfoca en la aplicación y composición de funciones que reciben entradas y generan nuevas salidas sin producir efectos secundarios.

A lenguajes como Java (a partir de su versión 8) o C++ moderno se les denomina multi-paradigma porque combinan las características del paradigma orientado a objetos clásico con elementos del paradigma funcional. Esto permite modelar el estado y las estructuras de datos mediante clases, herencia y polimorfismo, mientras que simultáneamente se emplean expresiones lambda y operaciones funcionales para manipular colecciones de datos de forma declarativa, combinando lo mejor de ambos enfoques según el problema a resolver.

Que las funciones sean consideradas "ciudadanos de primera clase" significa que el lenguaje de programación trata a las funciones de la misma manera que a cualquier otro valor o variable fundamental (como un entero o una cadena). Es decir, las funciones pueden ser asignadas a variables, pasadas como argumentos a otras funciones, devueltas como resultado de otras funciones y almacenadas en estructuras de datos. Esta característica es la base que permite la creación de funciones de orden superior y la programación declarativa.


## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta
La sintaxis de una expresión lambda en Java se compone fundamentalmente de tres partes: una lista de parámetros separada por comas y encerrada entre paréntesis, el operador flecha -> (que se lee como "se convierte en" o "produce"), y un cuerpo que puede ser una única expresión o un bloque de código encerrado entre llaves. La estructura general es (parametros) -> { cuerpo }.

Gracias a las capacidades de inferencia de tipos del compilador de Java, en la mayoría de los casos no es necesario especificar el tipo de dato de los parámetros en la expresión lambda; el compilador los deduce a partir del contexto donde se esté utilizando (por ejemplo, del tipo de la interfaz a la que se asigne). Si hay un solo parámetro, se pueden omitir los paréntesis parametro -> { cuerpo }. Si no hay parámetros, se utilizan paréntesis vacíos () -> { cuerpo }.

Respecto al cuerpo de la función, si está compuesto por una única sentencia cuya evaluación es el valor de retorno de la lambda, se pueden omitir las llaves y la palabra reservada return. Sin embargo, si el cuerpo consta de múltiples líneas, las llaves son obligatorias, así como el uso explícito de return si se espera que la función devuelva un valor.

## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta
La capacidad de recibir funciones como parámetros es lo que define a las denominadas "funciones de orden superior". Al hacer esto, se logra abstraer el algoritmo principal y delegar comportamientos específicos a quien invoca el método. En el caso de transformar una cadena, el método transformar se encarga de recibir la entrada, pero desconoce qué tipo de transformación se va a aplicar, lo cual promueve un alto grado de desacoplamiento y reutilización de código.

A continuación, se muestra cómo se implementa este patrón en ambos lenguajes. Se define la función transformar que espera los datos a operar y la lógica de operación, para después invocarla internamente.

```java script

// Función que recibe otra función como parámetro
function transformar(texto, funcionTransformadora) {
    return funcionTransformadora(texto);
}

const aMayusculas = (cadena) => cadena.toUpperCase();

let miTexto = "hola a todos";
// Se pasa la función como un argumento
let resultado = transformar(miTexto, aMayusculas);
console.log(resultado);

```java
import java.util.function.Function;

public class Main {
    
    // Método que recibe una interfaz funcional (lambda) como parámetro
    public static String transformar(String texto, Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(texto);
    }

    public static void main(String[] args) {
        Function<String, String> aMayusculas = cadena -> cadena.toUpperCase();
        
        String miTexto = "hola a todos";
        // Se pasa la variable funcional como argumento
        String resultado = transformar(miTexto, aMayusculas);
        System.out.println(resultado);
    }
}


## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta
Una de las mayores ventajas de las expresiones lambda es que permiten definir el comportamiento "al vuelo", directamente en el punto donde se necesita. Esto elimina la necesidad de declarar variables o métodos separados para lógicas que únicamente se van a utilizar en ese contexto específico. Al pasar la lambda directamente como parámetro, el código resulta más compacto y fácil de leer, ya que la lógica y la invocación ocurren en la misma línea.

Cuando se inyecta la función de manera directa en la llamada a transformar, el compilador (en el caso de Java) evalúa el tipo del parámetro esperado por la firma del método. Al detectar que se requiere una Function<String, String>, infiere automáticamente que la lambda proporcionada debe tomar un String y retornar otro String, verificando la compatibilidad de tipos sin que el desarrollador tenga que escribir las definiciones formales.

´´´javaScript
// Usando la función 'transformar' previamente definida
let texto = "hola";

// Lambda definida inline que invierte la cadena
let invertido = transformar(texto, (cadena) => cadena.split('').reverse().join(''));

console.log(invertido); // Muestra "aloh"

```java
// Suponiendo que el método 'transformar' está definido en la clase
public class Main {
    // ... (método transformar definido antes)

    public static void main(String[] args) {
        String texto = "hola";
        
        // Lambda definida inline que invierte la cadena
        String invertido = transformar(texto, cadena -> new StringBuilder(cadena).reverse().toString());
        
        System.out.println(invertido); // Muestra "aloh"
    }
}


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta
Un cierre, o "closure", es una característica que permite a una función lambda "capturar" y acceder a las variables del ámbito (scope) local en el que fue declarada, incluso después de que la ejecución haya salido de ese ámbito. A diferencia de las funciones regulares que solo conocen sus propios parámetros y las variables globales, una lambda con closure envuelve tanto el código a ejecutar como una instantánea del estado de las variables locales circundantes en el momento de su creación.

En Java, existe una regla estricta respecto a los closures: las variables locales capturadas por una expresión lambda deben ser "efectivamente finales". Esto significa que una vez que a la variable local se le ha asignado un valor, dicho valor no puede volver a modificarse ni dentro ni fuera de la función lambda. Si se intenta alterar el valor de una variable capturada, el compilador lanzará un error. Esta restricción asegura que la ejecución sea predecible y evita problemas de concurrencia.

A continuación, se muestra cómo una función lambda enviada al método transformar utiliza una variable definida externamente a la función para concatenar texto.

```java
import java.util.function.Function;

public class Main {
    
    public static String transformar(String texto, Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(texto);
    }

    public static void main(String[] args) {
        String textoInicial = "Mensaje";
        
        // Variable local en el ámbito de main. 
        // No se modifica después de declararse, por lo que es "efectivamente final".
        String sufijoExterno = " completado con éxito!";
        
        // La lambda actúa como closure capturando 'sufijoExterno'
        Function<String, String> agregarSufijo = cadena -> cadena + sufijoExterno;
        
        String resultado = transformar(textoInicial, agregarSufijo);
        System.out.println(resultado); // Muestra "Mensaje completado con éxito!"
    }
}


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta
La diferencia más fundamental radica en la gestión del contexto y el estado a través de los closures. Un puntero a función en C es simplemente una dirección de memoria estática que apunta a un bloque de instrucciones compiladas; no tiene memoria de su entorno. Si una función en C necesita datos externos, se le deben pasar obligatoriamente como parámetros. Por el contrario, una función lambda encapsula tanto el comportamiento (código) como el contexto léxico (estado capturado de las variables circundantes). Esto hace que las lambdas actúen casi como pequeños objetos que portan estado interno y un único método ejecutable.

Otra diferencia clave es el diseño a nivel de sistema de tipos y la orientación a objetos. En C, los punteros a funciones operan a bajo nivel. En lenguajes como Java, una expresión lambda es esencialmente "azúcar sintáctico" que el compilador transforma internamente en una instancia de una clase anónima que implementa una interfaz funcional específica. Esto permite que las lambdas interactúen de manera natural con el polimorfismo, las colecciones genéricas y el sistema estático de tipos del entorno orientado a objetos.

Finalmente, desde el punto de vista sintáctico y de diseño, las funciones lambda suelen ser anónimas y se definen "en línea" justo en el lugar donde se emplean. En C, toda función apuntada debe haber sido definida formalmente en otro lugar con un nombre (incluso si solo se usa una vez), lo cual genera dispersión en el código. La flexibilidad sintáctica de las lambdas promueve un estilo de programación mucho más fluido y declarativo.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta
Devolver funciones como resultado de otras funciones es otro pilar de la programación funcional que permite construir "fábricas de funciones". Esto permite configurar comportamientos base pasándoles ciertos parámetros iniciales y obteniendo a cambio funciones altamente especializadas listas para operar. En lugar de crear un único método que reciba tanto el porcentaje como la cantidad en cada llamada, se separa la lógica: primero se configura la función con su porcentaje, y luego esta función retenida se aplica repetidamente sobre diferentes cantidades.

La importancia de la closure (cierre) se hace especialmente evidente aquí. Cuando el método crearDescuento se ejecuta, toma el parámetro porcentaje, crea la lambda que lo utiliza, y finaliza su ejecución. En un escenario normal, las variables locales y parámetros se destruyen tras terminar la función constructora. Sin embargo, gracias al closure, la función devuelta "recuerda" y retiene el valor del porcentaje con el que fue creada. Cada instancia de descuento creada encapsula en su interior un estado invariable de su propio porcentaje particular, viviendo independientemente del contexto que la originó.

A continuación, se muestra el código en Java que crea estas fábricas de descuento y las utiliza sobre diferentes montos.

```java
import java.util.function.Function;

public class Main {
    
    // Método fábrica que retorna una función lambda
    public static Function<Double, Double> crearDescuento(double porcentaje) {
        // La lambda capturará el parámetro 'porcentaje'
        return precioBase -> precioBase - (precioBase * (porcentaje / 100.0));
    }

    public static void main(String[] args) {
        // Se crean dos funciones especializadas gracias a la retención del estado (closure)
        Function<Double, Double> descuento10 = crearDescuento(10.0);
        Function<Double, Double> descuento25 = crearDescuento(25.0);
        
        double precioProducto = 200.0;
        
        System.out.println("Precio con 10% de descuento: " + descuento10.apply(precioProducto)); // 180.0
        System.out.println("Precio con 25% de descuento: " + descuento25.apply(precioProducto)); // 150.0
    }
}


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta
En Java, debido a su fuerte tipado estático, una expresión lambda no puede existir flotando libremente sin un tipo de dato reconocido por el compilador. Para solucionar esto se emplean las interfaces funcionales. Una interfaz funcional es simplemente cualquier interfaz que defina exactamente un único método abstracto. Cuando se escribe una función lambda, el compilador infiere que esa lambda es la implementación concreta de ese único método abstracto, y el tipo de la lambda se convierte en el tipo de la interfaz funcional correspondiente.

El requisito estricto y único de una interfaz funcional es que posea un solo método abstracto sin implementar. Esta característica se conoce frecuentemente como SAM (Single Abstract Method). Si una interfaz tiene dos o más métodos abstractos, el compilador sería incapaz de deducir a cuál de ellos intenta dar respuesta la lambda.

Es importante destacar que las interfaces funcionales pueden tener múltiples métodos default o métodos static (ambos ya implementados con cuerpo), así como métodos abstractos que sobrescriban métodos de la clase Object (como equals o toString). La presencia de estos métodos adicionales no rompe la regla, siempre y cuando se mantenga exactamente la existencia de un solo método abstracto propio por implementar. Además, suele utilizarse la anotación opcional @FunctionalInterface para indicar explícitamente el propósito de la interfaz y forzar al compilador a lanzar un error si accidentalmente se añaden más métodos abstractos.


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta
La creación manual de una interfaz funcional implica definir la interfaz con la anotación correspondiente por buenas prácticas, y establecer un único método abstracto cuya firma se corresponda con las entradas y salidas de la función lambda que se pretende modelar. Al hacerlo de este modo, se define un contrato claro y específico para el dominio del problema.

Para el caso del transformador de cadenas, se necesita un método que reciba un String por parámetro y devuelva otro String. A este método se le puede asignar cualquier nombre representativo, ya que, al invocar la lambda, Java buscará simplemente el único método abstracto disponible.

```java
// La anotación verifica en tiempo de compilación que cumple los requisitos SAM
@FunctionalInterface
public interface Transformador {
    
    // Único método abstracto. Recibe un String y devuelve un String.
    String transformar(String texto);
    
}


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta
El uso de tipos genéricos (Generics) incrementa exponencialmente la utilidad de las interfaces funcionales personalizadas. En lugar de estar atada a trabajar estrictamente con String, se puede modificar la interfaz paramatrizando el tipo de entrada y el tipo de salida, representados por letras convencionales como T (Tipo de entrada) y R (Tipo de Retorno). De esta forma, la interfaz puede reutilizarse para cualquier tipo de transformación requerida en el código.

Al instanciar la lambda, se determinan los tipos concretos de T y R, y el compilador garantiza la seguridad de tipos, impidiendo operaciones ilógicas sin necesidad de recurrir a "casteos" inseguros que podrían resultar en excepciones en tiempo de ejecución.

```java
// Interfaz funcional genérica
@FunctionalInterface
public interface Transformador<T, R> {
    // Recibe un tipo T y retorna un tipo R
    R transformar(T entrada);
}

// En el método principal o de uso:
public class Main {
    public static void main(String[] args) {
        
        // Se define un transformador indicando que T es Double y R es Integer
        Transformador<Double, Integer> redondear = decimal -> (int) Math.round(decimal);
        
        Double numero = 15.6;
        Integer redondeado = redondear.transformar(numero);
        
        System.out.println("Valor original: " + numero);
        System.out.println("Valor redondeado: " + redondeado);
    }
}


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta
Dado que ciertos patrones de firma de métodos (recibir un dato y devolver otro, recibir y no devolver, etc.) se repiten constantemente en la programación funcional, el ecosistema de Java (a partir del paquete java.util.function) incluye un extenso conjunto de interfaces funcionales predefinidas y fuertemente tipadas mediante genéricos. Esto estandariza la escritura de lambdas sin que el desarrollador necesite crear sus propias interfaces personalizadas como la del ejemplo anterior.

Las familias principales de interfaces funcionales predefinidas se clasifican de la siguiente manera:

Function<T, R>: Representa una función que acepta un argumento de tipo T y produce un resultado de tipo R. Su método es R apply(T t).

Consumer<T>: Representa una operación que acepta un único argumento de entrada y no devuelve ningún resultado (generalmente se usa para efectos secundarios, como imprimir o modificar propiedades). Su método es void accept(T t).

Supplier<T>: Representa un proveedor de resultados. No toma argumentos, pero devuelve un valor de tipo T, útil para inicialización perezosa o generación de datos. Su método es T get().

Predicate<T>: Representa una función booleana evaluadora para un argumento dado de tipo T. Es la base para filtros lógicos en colecciones. Su método es boolean test(T t).

Adicionalmente, existen variaciones binarias (como BiFunction<T, U, R>, BiConsumer<T, U>) y variaciones especializadas en tipos primitivos para evitar la penalización de rendimiento del "boxing/unboxing" en memoria (como IntFunction<R>, DoubleConsumer, IntPredicate, etc.).


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta
El uso de List.forEach introduce un cambio sutil pero profundo en cómo se concibe la iteración: se transita de una iteración externa (donde el desarrollador gestiona manualmente los índices o el bucle for-each estructurado) a una iteración interna. En la iteración interna, la propia colección sabe cómo recorrer sus elementos, y simplemente se le proporciona una función Consumer con la lógica a ejecutar sobre cada elemento por individual.

Este patrón reduce considerablemente el código repetitivo de control (boilerplate), minimiza los errores lógicos, como los fallos en los límites de los arreglos ("off-by-one errors"), y permite leer el código de manera fluida, enfocándose directamente en "qué" se debe hacer sobre cada ítem en lugar de "cómo" avanzar al siguiente ítem.

A continuación, se muestra el recorrido de una lista mediante el método forEach empleando una lambda que actúa como Consumer.

```java
import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> numeros = Arrays.asList(-3, 5, 0, 12, -8, 7);
        
        // Iteración funcional usando forEach. 
        // Recibe una lambda que actúa como un Consumer<Integer>.
        numeros.forEach(n -> {
            if (n > 0) {
                System.out.println("El número " + n + " es positivo.");
            }
        });
    }
}

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta
La firma Consumer<? super T> en lugar del estricto Consumer<T> se basa en la flexibilización de los límites de los genéricos para admitir jerarquías de herencia de manera intuitiva. PECS es un acrónimo que significa Producer Extends, Consumer Super. Es un principio fundamental diseñado para determinar el uso de comodines (wildcards) en colecciones y funciones genéricas en Java. Si un objeto se va a utilizar únicamente como productor de datos, se debe usar ? extends T. Si se va a utilizar como consumidor para recibir datos, se debe usar ? super T.

En el caso de forEach sobre una List<Integer>, la lista está emitiendo (produciendo) datos de tipo Integer. La lambda que recibe forEach es el consumidor de esos datos. Si se forzara a usar Consumer<T>, solo aceptaría lógicas diseñadas exclusivamente para Integer. Al utilizar Consumer<? super T>, se hace posible enviar una función capaz de procesar la clase base, por ejemplo, un Consumer<Number> o Consumer<Object>. Puesto que un Integer es un Number y un Object, cualquier lógica general concebida para clases superiores procesará un Integer con seguridad sin violar el sistema de tipos.

Al aplicar el principio PECS para mejorar el método genérico transformar(T entrada, Function<T, R> transformador), debemos observar sus roles. El transformador es una función que consume un tipo T (lo recibe de entrada) y produce un tipo R (lo devuelve). Por lo tanto, para lograr la máxima flexibilidad de herencia, el método óptimo debería definirse como:
public static <T, R> R transformar(T entrada, Function<? super T, ? extends R> transformador). Esto permite que a la función transformar se le pase un argumento de tipo String, y aplique sobre él un transformador que haya sido concebido genéricamente para procesar un simple Object (? super T), generando por ejemplo una clase ArrayList, a pesar de que se espere retornar genéricamente una interface Collection (? extends R).

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta
Las referencias a métodos permiten tratar funciones ya existentes como si fueran expresiones lambda, facilitando su paso como variables o argumentos. Frecuentemente se da el escenario donde la única acción ejecutada en el cuerpo de una lambda es llamar a otro método (x -> obj.metodo(x)). En lugar de escribir esa sintaxis redundante, las referencias extraen y delegan el comportamiento a dicho método.

Un matiz importante es el contexto de invocación (el valor del objeto o del operador this). En JavaScript, cuando se extrae un método de su objeto, se pierde su enlace original, por lo que suele requerirse explícitamente el uso de bind() para ligarlo a la instancia. En Java, el uso del operador especial :: sobre la instancia ya resuelve internamente este anclaje contextual en tiempo de compilación.

´´´javaScript
class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }
    saludar() {
        console.log("Hola, soy " + this.nombre);
    }
}

let persona = new Persona("Ana");

// Referencia al método. Se usa bind para no perder el contexto 'this' de la instancia Ana
let refSaludar = persona.saludar.bind(persona);

// Invocación a través de la referencia
refSaludar();

´´´java
// Se importa interfaz funcional para representar un método sin parámetros
import java.lang.Runnable;

class Persona {
    private String nombre;
    public Persona(String nombre) { this.nombre = nombre; }
    public void saludar() { System.out.println("Hola, soy " + this.nombre); }
}

public class Main {
    public static void main(String[] args) {
        Persona persona = new Persona("Ana");
        
        // Referencia a un método de una instancia concreta en Java usando '::'
        // Runnable es una interfaz funcional de tipo () -> void
        Runnable refSaludar = persona::saludar;
        
        // Invocación ejecutando la interfaz
        refSaludar.run();
    }
}

## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta
En Java existen cuatro tipos fundamentales de referencias a métodos, categorizados según a qué clase o a qué objeto pertenecen, empleando siempre el operador :: para extraer el puntero metodológico correspondiente.

Referencia a un método estático: Llama a métodos de clase que no dependen de ningún objeto instanciado. Su sintaxis es Clase::metodoEstatico.

Referencia a un constructor: Se emplea como fábrica para instanciar objetos usando la palabra reservada new. Su sintaxis es Clase::new.

Referencia a un método de una instancia concreta: (Objeto Limitado / Bounded). Delega la ejecución en un objeto ya existente declarado en el entorno de la aplicación. Su sintaxis es instancia::metodo.

Referencia a un método de instancia sobre un objeto arbitrario de un tipo específico: (Objeto No Limitado / Unbounded). En este escenario, el objeto objetivo aún no se conoce al definir la referencia; el objeto será el primer parámetro que se pase al invocar la lambda (por ejemplo: (obj) -> obj.metodo()). Su sintaxis es Clase::metodoInstancia.

A continuación, se presentan ejemplos de cada uno empleando las interfaces funcionales estandarizadas de Java:

import java.util.function.*;

public class Main {
    public static void main(String[] args) {
        // 1. Método Estático (Math.max(a, b))
        BiFunction<Integer, Integer, Integer> maxEstatico = Math::max;
        
        // 2. Método de Constructor (new String())
        Supplier<String> crearStringVacio = String::new;
        
        // 3. Método de una instancia concreta (System.out.println(x))
        Consumer<String> imprimir = System.out::println;
        
        // 4. Método de instancia de un objeto arbitrario (str.toLowerCase())
        // El String en el que se ejecuta será el que se reciba como argumento
        Function<String, String> aMinusculas = String::toLowerCase;
    }
}


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta
El ordenamiento de objetos personalizados es un área en la que las expresiones lambda combinadas con métodos de la interfaz Comparator muestran de manera brillante la evolución hacia un código más declarativo y limpio. La lógica tradicional implica realizar ramificaciones manuales (bloques if/else) analizando las propiedades una por una y gestionando devoluciones de números enteros (negativo, cero, positivo), lo cual suele ser propenso a errores al manejar empates e igualdades.

Por otra parte, la clase Comparator en las últimas versiones de Java ha incorporado métodos estáticos y métodos por defecto (default) que, empleando referencias a métodos y lambdas, permiten encadenar combinadores lógicos, creando ordenamientos multidimensionales de forma que el código se lee prácticamente como lenguaje natural.

A continuación, se ilustran ambas versiones abordando el mismo problema de ordenamiento múltiple.

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class Persona {
    private String nombre;
    private int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public String getNombre() { return nombre; }
    public int getEdad() { return edad; }
    public String toString() { return nombre + " (" + edad + ")"; }
}

public class Main {
    public static void main(String[] args) {
        List<Persona> personas = Arrays.asList(
            new Persona("Carlos", 30),
            new Persona("Ana", 25),
            new Persona("Beatriz", 25)
        );

        // --- VERSIÓN 1: Lambda manual (estilo imperativo/bloque IF) ---
        Collections.sort(personas, (p1, p2) -> {
            int compEdad = Integer.compare(p1.getEdad(), p2.getEdad());
            if (compEdad != 0) {
                return compEdad;
            }
            return p1.getNombre().compareTo(p2.getNombre());
        });
        System.out.println("Orden Lambda Manual: " + personas);


        // Se desordena temporalmente para probar la versión 2
        Collections.shuffle(personas);


        // --- VERSIÓN 2: Empleando combinadores de Comparator y referencias a método ---
        // (Enfoque declarativo, mucho más limpio)
        personas.sort(Comparator.comparingInt(Persona::getEdad)
                                .thenComparing(Persona::getNombre));
                                
        System.out.println("Orden Comparator Combinado: " + personas);
    }
}
```</T,></T,></Object></Number></T></Integer></T></T></R></T,></T,></T></T></T></T,></T,></Double,></String,></String,>
