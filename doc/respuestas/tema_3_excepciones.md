<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Respuesta

En lenguajes procedimentales clásicos como C, la gestión de errores se basa en convenciones manuales implementadas por el programador, ya que no existe un mecanismo nativo en el núcleo del lenguaje para interrumpir el flujo ante una anomalía. Cuando una función matemática recibe un valor inválido, como un radicando negativo, debe notificar al código llamador utilizando los canales de comunicación estándar de las funciones.

La primera opción de diseño es utilizar el propio valor de retorno para indicar el error, devolviendo un "valor centinela". En el caso de una raíz cuadrada, dado que el resultado matemático en el dominio de los números reales nunca puede ser negativo, se podría devolver `-1.0` para señalizar un fallo estructural, obligando al invocador a evaluar el resultado mediante una sentencia condicional.

```c
#include <stdio.h>
#include <math.h>

// Opción 1: Retorno de valor centinela
double raiz_opcion1(double numero) {
    if (numero < 0) {
        return -1.0; // Código de error
    }
    return sqrt(numero);
}

int main() {
    double resultado = raiz_opcion1(-4.0);
    if (resultado == -1.0) {
        printf("Error: No se puede calcular la raiz de un negativo.\n");
    }
    return 0;
}

```

La segunda opción consiste en separar la notificación del estado de la operación del resultado matemático utilizando punteros (paso por referencia). La función devuelve un código de estado en formato entero (por ejemplo, `0` para indicar éxito, `1` para error), mientras que el resultado del cálculo se deposita de forma segura en una variable externa cuya dirección de memoria se ha proporcionado como parámetro.

```c
#include <stdio.h>
#include <math.h>

// Opción 2: Código de error por retorno, resultado por puntero
int raiz_opcion2(double numero, double *resultado) {
    if (numero < 0) {
        return 1; // 1 indica error
    }
    *resultado = sqrt(numero);
    return 0; // 0 indica éxito
}

int main() {
    double res;
    int estado = raiz_opcion2(-9.0, &res);
    
    if (estado != 0) {
        printf("Error: Argumento invalido proporcionado a la funcion.\n");
    } else {
        printf("El resultado es: %f\n", res);
    }
    return 0;
}

```

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Respuesta

Una excepción es un evento anómalo o inesperado que ocurre durante la ejecución de un programa y que rompe el flujo normal y secuencial de las instrucciones. Representa una condición de error (estructural, lógica o dependiente del hardware) que el bloque de código actual no está capacitado o diseñado para resolver por sí mismo.

El objetivo para el programador que implementa la función (quien origina o "lanza" la excepción) es disponer de un mecanismo arquitectónico seguro para abortar la operación actual de forma inmediata. Esto evita devolver valores corruptos o códigos de error silenciosos, delegando la responsabilidad y la toma de decisiones al entorno superior que invocó dicha función.

Para el programador que llama a la función, el objetivo es mantener el código limpio y mantenible, separando drásticamente el "camino feliz" (la lógica principal de negocio) de la lógica de gestión de errores. En lugar de plagar el programa con sentencias `if` de comprobación tras cada llamada (como ocurre en C), se agrupa la recuperación de fallos en zonas designadas y delimitadas, mejorando la legibilidad.

## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Respuesta

En Java, la notificación de un error se materializa instanciando y arrojando un objeto de excepción. Al detectar el escenario anómalo (el número negativo), la función de la calculadora no devuelve códigos ni modifica punteros, sino que interrumpe su ciclo empleando la instrucción `throw` junto con una excepción predefinida en la biblioteca estándar que represente el problema.

El método llamador se hace cargo del control de daños instaurando un bloque estructural `try-catch`. Las operaciones susceptibles de fallar se envuelven en el segmento `try`, y la respuesta al error queda aislada en el segmento `catch`, impidiendo que el fallo alcance la Máquina Virtual y colapse el programa entero.

```java
class Calculadora {
    // Método que calcula la raíz o aborta mediante una excepción
    public double raiz(double numero) {
        if (numero < 0) {
            // Se lanza un objeto que representa un argumento ilegal
            throw new IllegalArgumentException("El numero no puede ser negativo");
        }
        return Math.sqrt(numero);
    }
}

public class Programa {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        
        // Se aísla el código de riesgo
        try {
            // Si esto falla, el flujo salta instantáneamente al bloque catch
            double resultado = calc.raiz(-16.0);
            System.out.println("El resultado es: " + resultado);
        } catch (IllegalArgumentException e) {
            // El error se controla externamente y el programa no se cuelga
            System.out.println("Error detectado desde el exterior: " + e.getMessage());
        }
    }
}

```

## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Respuesta

"Lanzar" una excepción es el acto de instanciar un objeto de error y entregarlo al sistema de ejecución, provocando la interrupción inmediata del flujo normal del código en ese punto. "Controlar" o "capturar" implica definir una zona de contención (el bloque `catch`) destinada explícitamente a interceptar ese objeto de error específico para ejecutar un plan de contingencia o notificación en su lugar.

La "propagación" es el proceso dinámico y automático que se desencadena cuando una excepción es lanzada pero la función actual no posee un bloque diseñado para capturarla. El sistema operativo virtual descarta la función actual y retrocede el control a la función llamadora en la pila (*stack*). Si la función llamadora tampoco la captura, el error continúa ascendiendo capa tras capa hasta encontrar un manejador válido o hasta estrellar el programa.

A medida que la excepción se propaga ascendiendo por la pila de llamadas, las funciones que son abandonadas experimentan una destrucción abrupta de su contexto local. Las variables almacenadas en esos segmentos de la memoria Stack son liberadas de inmediato.

Es imperativo comprender que las funciones interrumpidas **jamás se reanudan**. En el ejemplo anterior, si hubiese más sentencias matemáticas programadas debajo de la línea del `throw` dentro del método `raiz`, dichas instrucciones quedarían permanentemente sin ejecutar. Del mismo modo, cualquier asignación pendiente en el método `main` tras la llamada a la función se cancela y se salta al bloque de gestión.

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

### Respuesta

La principal ventaja de la propagación natural es que erradica la necesidad de codificar una burocracia repetitiva y propenso a descuidos para escalar los errores a lo largo de las distintas capas del software. En lenguajes como C, si una función de bajo nivel falla, requiere que todas y cada una de las funciones intermedias hasta la capa superior incluyan lógica condicional explícita para recoger el código de error numérico y pasarlo al siguiente nivel.

Con el sistema de excepciones, este transporte es completamente automático. Las funciones intermedias pueden centrarse de manera exclusiva en su lógica operativa sin incluir ni una sola línea de código destinada al manejo de fallos si no tienen jurisdicción para resolverlos. El error atravesará esas funciones de manera invisible hasta alcanzar el componente superior diseñado para tratarlo.

Adicionalmente, este modelo garantiza que los errores estructurales no puedan ser silenciados por omisión. En C, si el desarrollador olvida colocar el `if` para verificar el valor devuelto por la función, el programa prosigue utilizando datos corruptos, desencadenando comportamientos erráticos. En Java, ignorar una excepción causa que esta se propague hasta el final, forzando una terminación controlada y ruidosa que previene daños colaterales.

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

### Respuesta

Efectivamente, en el paradigma de programación orientada a objetos, las excepciones son objetos de pleno derecho que descienden de una jerarquía de clases predeterminada (en Java, todas heredan de la clase base `Throwable`). Al igual que cualquier otra instancia, residen en la porción de memoria dinámica, gozan de estado interno mediante atributos encapsulados y ofrecen un catálogo de métodos como interfaz pública.

Esta naturaleza estructural confiere ventajas sustanciales sobre el uso de valores primitivos. En lugar de limitar la información del fallo a un mero código numérico (como ocurre con la variable global `errno` en entornos UNIX), un objeto de excepción agrupa y encapsula todo el contexto ambiental del error: cadenas de texto descriptivas, estados de variables problemáticas, e incluso objetos de otras excepciones previas que originaron el problema actual.

Al tratarse de clases estándar, la arquitectura fomenta la creación de excepciones completamente personalizadas. Cualquier desarrollador puede definir nuevas entidades creando una clase que herede de `Exception`, dotándola de atributos que representen infracciones de las reglas de negocio específicas. Así, se podría modelar una `TemperaturaExcesivaException` que capture internamente y de forma privada el grado térmico exacto en el que falló un sensor.

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

### Respuesta

Un objeto excepción estándar en Java transporta de forma automática dos activos de información invaluables que superan con creces la utilidad de un código entero devuelto en C. La primera pieza es un **mensaje descriptivo textual**. Este atributo interno encapsulado, accesible externamente mediante el método `getMessage()`, provee una aclaración lingüística sobre el motivo que desencadenó el fallo, ofreciendo contexto humano sin requerir la consulta de manuales externos de códigos de error.

La segunda pieza, de importancia superlativa para la depuración de sistemas complejos, es la **traza de la pila** o *stack trace*. Al momento de ser instanciada con el comando `new`, la excepción toma una "fotografía" del estado exacto de la memoria.

Esta traza incluye una enumeración cronológica inversa de todos los métodos por los que atravesó el hilo de ejecución, reportando el nombre del archivo fuente, el nombre de la clase, y el número exacto de la línea de código donde se provocó la avería y por donde se propagó. Esto permite localizar la falla instantáneamente en un proyecto con miles de archivos.

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta

El diseño sintáctico de Java permite estructurar un único segmento `try` continuado por una multiplicidad de bloques `catch` encadenados. Esta disposición técnica faculta al desarrollador para atrapar diversas familias de errores que pudieran emanar de la misma porción de código, permitiendo aplicar contramedidas específicas y diferenciadas para cada escenario (por ejemplo, actuar de un modo ante un error aritmético y de otro ante una desconexión de red).

De toda esa cadena de bloques de captura dispuestos secuencialmente, **solo se ejecutará uno como máximo**. En el instante en que el objeto anómalo emerge del bloque principal, la Máquina Virtual desciende por la lista de manejadores y selecciona el primero que encaje topológicamente con la clase de la excepción arrojada.

Una vez que se ejecuta el código de contención del manejador victorioso, el ecosistema ignora por completo la existencia de los bloques `catch` subsiguientes y el flujo operativo se reanuda más allá de la estructura al completo. Por esta regla de evaluación, es estrictamente obligatorio colocar los manejadores que atrapan excepciones muy específicas antes de aquellos que atrapan excepciones genéricas de más alto nivel jerárquico.

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta

Para neutralizar la incertidumbre causada por las rupturas abruptas del ciclo de control y salvaguardar la gestión de recursos críticos (cierres de archivos, clausuras de puertos de base de datos), se integra la cláusula de seguridad `finally`. Este bloque residual se dispone como conclusión ineludible de la estructura protectora y tiene como propósito ejecutar tareas de limpieza mandatorias.

Las instrucciones alojadas en el seno del bloque `finally` gozan de garantía absoluta de ejecución. El entorno virtual suspenderá momentáneamente cualquier intento de abortar la función, propagar el error o devolver un resultado, para dar prioridad al código de cierre estipulado en dicha cláusula.

```java
public class ArchivoGestor {
    
    // Ejemplo 1: Estructura completa (try-catch-finally)
    public void leerConCaptura() {
        try {
            System.out.println("Abriendo recurso protegido...");
            throw new RuntimeException("Error simulado de lectura");
        } catch (RuntimeException e) {
            System.out.println("Gestionando el fallo: " + e.getMessage());
        } finally {
            // Este código se ejecuta siempre, haya error o no.
            System.out.println("Cerrando recurso y liberando memoria.");
        }
    }

    // Ejemplo 2: Estructura de delegación (try-finally sin catch)
    public void escribirSinCaptura() {
        try {
            System.out.println("Abriendo recurso de escritura...");
            // Si ocurre un error, no se atrapa aquí, se propagará al exterior
        } finally {
            // Pero antes de que el error abandone esta función, se asegura el cierre
            System.out.println("Cerrando recurso de escritura forzosamente.");
        }
    }
}

```

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta

Efectivamente, el segmento `finally` puede ensamblarse de forma directa al segmento `try` prescindiendo de cualquier bloque intermedio de contención. Esta configuración estructural es lícita e idónea en escenarios donde el método en curso no posee la responsabilidad arquitectónica para resolver la anomalía, pero sí recae sobre él la exigencia imperativa de higienizar los recursos alojados localmente antes de que el error ascienda hacia instancias superiores.

La naturaleza ontológica de este componente dicta que se ejecutará bajo absolutamente cualquier circunstancia. Se procesa de manera rutinaria si la porción de código riesgosa culmina su labor de manera impoluta, se ejecuta en caso de que se haya desencadenado un error capturado con éxito, y actúa también como último reducto de defensa si se dispara un error que no posee manejador.

Su inmutabilidad de ejecución es tan prevalente que domina sobre directivas de control explícitas. Si el desarrollador codifica una sentencia `return` en el interior del segmento protegido para forzar la evacuación del método, la arquitectura subyacente intercepta el retorno, transfiere el flujo para ejecutar íntegramente las instrucciones de saneamiento, y únicamente tras concluirlas, consuma la devolución del valor al exterior.

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta

Las excepciones **controladas** (conocidas en la literatura anglosajona como *checked exceptions*) son anomalías que el compilador exige tratar de manera coercitiva; si una subrutina es propensa a emitirlas, el código invocador queda forzado a aislarlas con un bloque de captura o a declarar que las delega. Las excepciones **no controladas** (*unchecked exceptions*), por contrapartida, eximen al desarrollador de esta imposición, asumiendo que el código base goza de un diseño lógico que previene su origen.

La clase estándar `RuntimeException` opera como la frontera divisoria de este ecosistema. El compilador clasifica dogmáticamente como no controladas a la totalidad de las clases que deriven de este linaje. Por exclusión, cualquier otra anomalía que descienda de la clase matriz y universal `Exception`, pero que esquive la rama de `RuntimeException`, queda automáticamente sometida al yugo de la comprobación estricta de sintaxis.

**Ejemplos de preferencia para Excepciones Controladas** (Sucesos anticipables del entorno operativo donde el sistema debe buscar alternativas de recuperación):

* Indisponibilidad temporal de una conexión al intentar alcanzar un servidor de datos externo.
* Intentos de lectura sobre un fichero de configuración crítico que el usuario final ha suprimido del disco.
* Interrupciones de flujo al alcanzar límites de tiempo (*timeouts*) durante procesos de descarga de red.

**Ejemplos de preferencia para Excepciones No Controladas** (Defectos puramente lógicos de la codificación que deben enmendarse reprogramando el software, no ocultándolos):

* Iteraciones de bucles que intentan franquear los límites máximos definidos en un vector matricial (`IndexOutOfBoundsException`).
* Solicitudes de ejecución de métodos invocadas sobre punteros sin inicializar o referencias vacías (`NullPointerException`).
* Fracturas aritméticas provocadas por divisiones por cero o violaciones del dominio numérico.

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta

La directiva semántica `throws` es una declaración explícita que se adhiere en la firma oficial de un método, acompañando a la definición de sus parámetros de entrada y retorno. Se emplea como un mecanismo de notificación pública para el resto del proyecto, advirtiendo de antemano qué tipologías de excepciones controladas ostentan la posibilidad de emerger desde el interior de dicha subrutina hacia capas exteriores.

Constituye la alternativa oficial y estandarizada frente a la captura de fallos. Cuando un método desencadena una instrucción propensa a anomalías, a menudo se encuentra en un nivel de abstracción demasiado bajo para tomar medidas correctivas competentes (como presentar una advertencia visual al usuario o cambiar parámetros de configuración dinámicos).

Al anexar la cláusula en la rúbrica del método, el programador declina toda obligación de apaciguar el error localmente. Esto apacigua las demandas restrictivas del compilador de Java y permite que la perturbación escale peldaños estructurales de manera natural, traspasando el problema y la toma de decisiones al estrato arquitectónico que solicitó la operación originaria.

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta

El siguiente fragmento ilustra cómo un método delega responsabilidades de corrección a la par que asume sus propios deberes de higienización. La firma advierte que cualquier percance de entrada/salida será ignorado a nivel local y expulsado al exterior, pero utiliza un entramado de bloques para velar por la clausura del recurso antes de consumar la evacuación del flujo.

Aunque en las iteraciones modernas del lenguaje Java esta configuración se automatiza mediante el paradigma "Try-with-Resources", su manifestación fundamental requiere la instanciación explícita del bloque residual de limpieza para evitar la fuga de descriptores de archivos de cara al sistema operativo subyacente.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class LectorDatos {
    
    // La firma advierte al exterior mediante "throws" sobre el riesgo de propagación
    public String procesarFichero(String ruta) throws IOException {
        BufferedReader lector = null;
        try {
            // Operación que puede detonar una IOException si la ruta no es válida
            lector = new BufferedReader(new FileReader(ruta));
            return lector.readLine();
            
        } finally {
            // El programador asegura el cierre de canales de comunicación siempre.
            // Si el lector no está vacío (hubo éxito al abrir), procedemos a cerrarlo.
            if (lector != null) {
                // El propio cierre puede lanzar otro fallo, pero por simplificación 
                // asumimos que el método principal se desentiende de ello.
                lector.close();
            }
        }
    }
}

```

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, los analizadores sintácticos y las reglas del compilador de Java facultan la posibilidad técnica de listar anomalías no controladas, como aquellas herederas de `RuntimeException` u otras afines, dentro de las declaraciones `throws` adjuntas a la cabecera de las funciones, ensamblándose la compilación final sin registrar amonestaciones restrictivas.

No obstante, esta inserción es meramente decorativa a nivel funcional; el método que invoca a la función en cuestión se mantendrá completamente exento de edificar zonas de captura de contención obligatoria. El ecosistema mantendrá la categorización laxa y la ejecución procederá inalterada sin exigir la redacción de bloques `try-catch` ni imposiciones burocráticas análogas sobre el código externo.

La validez arquitectónica de llevar a cabo esta práctica reposa íntegramente sobre labores de documentación técnica y aviso a navegantes. Estampar una disfunción no controlada en la cabecera sirve de advertencia humana y preventiva a otros ingenieros para informarles sobre precondiciones frágiles subyacentes, invitándolos a revisar exhaustivamente los parámetros introducidos en lugar de prepararse para atrapar la caída del sistema posterior.

---

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, técnicamente es posible declarar excepciones no controladas (como `RuntimeException` o cualquiera de sus derivadas) en la cláusula `throws` de la firma de un método en Java. El compilador aceptará esta sintaxis sin generar ningún tipo de error de compilación, integrando la declaración en la definición formal de la subrutina de manera natural.

Sin embargo, incluir una excepción no controlada en el `throws` no altera su naturaleza: no obliga en absoluto al método llamador a implementar un bloque `try-catch` ni a propagarla explícitamente en su propia firma. El compilador de Java seguirá tratando la excepción como opcional, permitiendo que el código invocador la ignore por completo, tal como dictan las reglas fundamentales de las excepciones no controladas.

El único sentido real y práctico de esta declaración es meramente informativo y de documentación. Al colocar una `RuntimeException` en la firma, se avisa explícitamente a otros programadores (a través de la generación automática de manuales como JavaDoc) sobre las posibles condiciones de fallo lógico asociadas al uso de esa función. Sirve como una advertencia humana sobre precondiciones que se deben cumplir al pasar los parámetros, en lugar de imponer una restricción técnica.

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta

Se recomienda utilizar excepciones controladas (como `IOException`) cuando el error se produce por factores externos e incontrolables para el programa, como la desconexión fortuita de una red, la falta de permisos en el disco duro o un archivo que el usuario ha borrado. En estos escenarios impredecibles, el sistema arquitectónico exige que el código tenga un plan de contingencia obligatorio y explícito para intentar recuperar la estabilidad o advertir al usuario de forma segura.

Por el contrario, las excepciones no controladas (como `IllegalArgumentException` o `NullPointerException`) se reservan para errores de programación, descuidos lógicos o "bugs". Son situaciones que deberían evitarse desde el principio escribiendo un código correcto, como asegurar que un puntero no sea nulo antes de desreferenciarlo o que no se accedan a índices fuera de los límites de un arreglo en memoria. Si ocurren, no se espera que el programa se recupere en tiempo de ejecución, sino que aborte para que el programador enmiende el defecto en el código fuente.

No todos los lenguajes orientados a objetos poseen esta dualidad técnica. De hecho, Java es una excepción en la industria al ser el lenguaje de uso general más prominente que implementa y fuerza el concepto de excepciones controladas a nivel de validación del compilador.

En la gran mayoría de lenguajes donde solo existe una opción (como C++, C#, Python, Ruby o JavaScript), todas las excepciones se comportan funcionalmente como "no controladas". La convención en estos ecosistemas es confiar en la documentación de las interfaces y en el buen criterio del programador para decidir cuándo y dónde colocar las zonas de captura, evitando la rigidez y el exceso de código de envoltura (boilerplate) que el modelo de Java suele requerir.

## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta

Sí, tiene perfecto sentido y es una estrategia de diseño muy común lanzar nuevas excepciones desde el interior de un bloque `catch`. Esto permite interceptar un error a bajo nivel puramente técnico, ocultar sus detalles de implementación, y emitir en su lugar un nuevo error semánticamente transformado que resulte más comprensible para las capas superiores del software o para la lógica de negocio.

Asimismo, es completamente viable relanzar exactamente la misma excepción original que acaba de ser capturada. Para lograrlo, simplemente se emplea la instrucción `throw` seguida de la variable referenciada en los parámetros del bloque `catch` actual (por ejemplo, `throw e;`), devolviendo el control de la anomalía a la pila de llamadas.

Relanzar la misma excepción cobra sentido cuando el método actual necesita interceptar el fallo temporalmente para realizar alguna acción lateral imperativa, como escribir en un archivo de registros (logs) o auditar la incidencia en una base de datos. Una vez que el sistema ha anotado la ocurrencia del error para su posterior análisis, no tiene capacidad de arreglar el problema real, por lo que permite que la anomalía original reanude su propagación ascendente.

```java
public void operacionArchivos() throws IOException {
    // Ejemplo 1: Lanzar una nueva excepción diferente dentro del catch
    try {
        int calculo = 100 / 0;
    } catch (ArithmeticException e) {
        // Se oculta el error matemático y se emite uno de argumento
        throw new IllegalArgumentException("Error procesando los parametros numéricos");
    }

    // Ejemplo 2: Relanzar exactamente la misma excepción
    try {
        FileReader f = new FileReader("configuracion_crítica.ini");
    } catch (IOException e) {
        // Se intercepta solo para auditoría
        System.out.println("LOG DE SISTEMA: Fallo grave al intentar abrir configuración.");
        // Se relanza la misma excepción para que la capa superior la gestione
        throw e; 
    }
}

```

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

El concepto de que una excepción sea la "causa" de otra (técnica conocida como encadenamiento de excepciones) consiste en atrapar un error de bajo nivel técnico y lanzar uno nuevo de nivel superior, pero preservando y adjuntando el objeto del error original dentro del nuevo. Esto permite abstraer el fallo a un lenguaje de negocio comprensible para la arquitectura superior, sin destruir la evidencia técnica subyacente que lo originó, lo cual es vital para el análisis forense del código.

En la programación en Java, casi todos los constructores de excepciones estándar aceptan un parámetro adicional de la clase genérica `Throwable`, reservado precisamente para albergar la causa. Al definir excepciones personalizadas de negocio, la práctica recomendada es suministrar siempre un constructor equivalente que reciba la excepción originaria (la causa) y se la transmita a la clase base utilizando la llamada `super()`.

Cuando una excepción encadenada no es controlada por ningún bloque y colapsa el hilo, imprimiendo la traza por pantalla (el *stack trace*), la causa original siempre se visualiza de forma explícita. La consola desglosa la excepción superior y, acto seguido, imprime un bloque separado introducido por la cadena de texto `"Caused by:"` (Causado por:), mostrando la traza completa de la anomalía técnica profunda que desencadenó el fallo de negocio.

```java
// Definición de una excepción de alto nivel (negocio)
class CargaUsuarioException extends Exception {
    // Constructor que acepta mensaje y la causa original
    public CargaUsuarioException(String mensaje, Throwable causa) {
        super(mensaje, causa); // Se delega el almacenamiento a la clase Exception
    }
}

public class GestorSistema {
    public void cargarPerfil() throws CargaUsuarioException {
        try {
            // Operación técnica propensa a fallos de bajo nivel
            FileReader f = new FileReader("C:/usuarios/admin.dat");
        } catch (IOException e) {
            // Se captura la IOException y se encapsula como causa de la nueva excepción
            throw new CargaUsuarioException("No fue posible cargar el perfil del administrador", e);
        }
    }
}

```