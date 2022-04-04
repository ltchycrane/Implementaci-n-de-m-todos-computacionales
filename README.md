<p align="center">
  <img src="https://user-images.githubusercontent.com/72751268/161465652-29e7749d-2ca6-468c-9c6e-3e851116af1f.jpg"/>
</p>

# Actividad 3.2 Programando un DFA
###### Sergio Alejandro Esparza González | A01625430
###### Eric Eugenio Oyervides Espino     | A01570760 
###### Alejandro Mauricio Maqueo Huerta  | A016206491
# Manual de Usuario



# Objetivo
Una de las aplicaciones de los autómatas finitos determinísticos es la implementación de reconocedores de tokens en un lenguaje de programación (conocido como Lexer en los compiladores).

En esta actividad deberás hacer un programa que reciba como entrada un archivo con una serie de expresiones aritméticas, escritas bajo ciertas reglas, y entregará como salida el conjunto de tokens reconocidos, indicando su tipo, o indicando que hay un error en su formación, es decir, no se respetaron las reglas establecidas.

# Lenguaje Utilizado
C++

# Recursos Necesarios
Para la utilización de este proyecto fue necesario el uso de un IDE o "Integrated Development Environment" ya sea de manera online o descargado.

```cpp
//Librerias:

#include <iostream>  //Flujo de datos.
#include <vector>    //Vectores.
#include <fstream>   //Archivos.
#include <string>    //Cadenas.

//Espacios de nombres:
using namespace std; //Estandar.

//Constantes:
const int ERROR = 18; //Estado de error.

//Variables globales:
ifstream inFile; //Leer datos del archivo.
int transitionMatrix[19][15] = 
  { 
  {6,     7,     8,     9,     10,    13,    11,    12,    14,    15,    15,    ERROR, ERROR, 0,     ERROR},
  {2,     2,     ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {5,     5,     ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, 1,     ERROR, 3,     0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 17,    ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 15,    ERROR, ERROR, ERROR, 15,    15,    15,    ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, 4,     ERROR, ERROR, 0,     ERROR},
  {17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17   },
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR}
  }; //Matriz de transiciones.
``` 
# Automata del problema
``` bash
{"type":"DFA","dfa":{"transitions":{"s0":{"1":"s15","4":"s18","5":"s19","6":"s20","7":"s2","8":"s4","9":"s5","+":"s6","-":"s7","=":"s8","*":"s9","^":"s10","(":"s11",")":"s12","/":"s14","e":"s1","E":"s1"," ":"s0"},"s18":{"8":"s13","9":"s13"},"s19":{"0":"s13","1":"s13","2":"s13","3":"s13","4":"s13","5":"s13","6":"s13","7":"s13"},"s20":{"5":"s1","6":"s1","7":"s1","8":"s1"},"s2":{"0":"s1","1":"s1","2":"s1","3":"s1","4":"s1","5":"s1","6":"s1","7":"s1","8":"s1","9":"s1"},"s15":{"0":"s16","1":"s21","2":"s22"},"s4":{"0":"s1","1":"s1","2":"s1","3":"s1","4":"s1","5":"s1","6":"s1","7":"s1","8":"s1","9":"s1"},"s5":{"0":"s1","7":"s1","8":"s1","9":"s1"},"s16":{"0":"s1","2":"s1","3":"s1","4":"s1","5":"s1","6":"s1","7":"s1","8":"s1","9":"s1"},"s21":{"0":"s1","1":"s1","2":"s1","3":"s1","4":"s1","5":"s1","6":"s1","7":"s1","8":"s1","9":"s1"},"s22":{"0":"s1","1":"s1","2":"s1"},"s3":{"4":"s23","5":"s25","+":"s17","-":"s17"," ":"s0"},"s23":{"8":"s26","9":"s26"},"s25":{"0":"s26","1":"s26","2":"s26","3":"s26","4":"s26","5":"s26","6":"s26","7":"s26"},"s17":{"4":"s23","5":"s25"," ":"s0"},"s27":{"4":"s23","5":"s25"," ":"s0"},"s28":{"4":"s23","5":"s25"," ":"s0","+":"s29","-":"s29"},"s29":{"4":"s23","5":"s25"," ":"s0"},"s6":{"4":"s18","5":"s19"," ":"s0"},"s7":{"4":"s18","5":"s19"," ":"s0"},"s8":{" ":"s0"},"s9":{" ":"s0"},"s10":{" ":"s0"},"s11":{" ":"s0"},"s12":{" ":"s0"},"s13":{"4":"s18","5":"s19","e":"s3","E":"s3"," ":"s0",".":"s27"},"s14":{" ":"s0","/":"s30"},"s30":{" ":"s30"},"s1":{"4":"s31","5":"s33"," ":"s0"},"s31":{"8":"s1","9":"s1"},"s33":{"0":"s1","1":"s1","2":"s1","3":"s1","4":"s1","5":"s1","6":"s1","7":"s1"},"s26":{"4":"s23","5":"s25"," ":"s0","e":"s28","E":"s28"}},"startState":"s0","acceptStates":["s1","s26","s13"]},"states":{"s0":{"top":255,"left":176,"displayId":"s0"},"s15":{"top":133,"left":630,"displayId":"100s"},"s18":{"top":333,"left":72,"displayId":"ts13"},"s19":{"top":348,"left":193,"displayId":"2ts13"},"s20":{"top":125.11111450195312,"left":53,"displayId":"ts15"},"s2":{"top":101,"left":181,"displayId":"70s"},"s4":{"top":100,"left":308,"displayId":"80s"},"s5":{"top":103,"left":418,"displayId":"90s"},"s6":{"top":216.11111450195312,"left":653,"displayId":"s6"},"s7":{"top":251,"left":856,"displayId":"s7"},"s8":{"top":320,"left":936,"displayId":"s8"},"s9":{"top":362,"left":933,"displayId":"s9"},"s10":{"top":404.3333435058594,"left":935,"displayId":"s10"},"s11":{"top":453,"left":938,"displayId":"s11"},"s12":{"top":503.11114501953125,"left":937,"displayId":"s12"},"s14":{"top":560.7777709960938,"left":924,"displayId":"s14"},"s1":{"isAccept":true,"top":34,"left":42,"displayId":"s15"},"s13":{"isAccept":true,"top":411.90277099609375,"left":130,"displayId":"s13"},"s16":{"top":86,"left":534,"displayId":"100s0"},"s21":{"top":36,"left":604,"displayId":"100s1"},"s22":{"top":20,"left":745,"displayId":"100s2"},"s3":{"top":479,"left":170,"displayId":"s1"},"s23":{"top":605,"left":99,"displayId":"ts16"},"s25":{"top":610,"left":227,"displayId":"2ts16"},"s17":{"top":482,"left":345,"displayId":"s2"},"s26":{"isAccept":true,"top":690,"left":172,"displayId":"s16"},"s27":{"top":504,"left":462,"displayId":"s3"},"s28":{"top":524.7777709960938,"left":574,"displayId":"s4"},"s29":{"top":559,"left":697,"displayId":"s5"},"s30":{"top":663,"left":842,"displayId":"s17"},"s31":{"top":145,"left":1004,"displayId":"tsc15"},"s33":{"top":145,"left":1113,"displayId":"2tsc15"}},"transitions":[{"stateA":"s0","label":"1","stateB":"s15"},{"stateA":"s0","label":"4","stateB":"s18"},{"stateA":"s0","label":"5","stateB":"s19"},{"stateA":"s0","label":"6","stateB":"s20"},{"stateA":"s0","label":"7","stateB":"s2"},{"stateA":"s0","label":"8","stateB":"s4"},{"stateA":"s0","label":"9","stateB":"s5"},{"stateA":"s0","label":"+","stateB":"s6"},{"stateA":"s0","label":"-","stateB":"s7"},{"stateA":"s0","label":"=","stateB":"s8"},{"stateA":"s0","label":"*","stateB":"s9"},{"stateA":"s0","label":"^","stateB":"s10"},{"stateA":"s0","label":"(","stateB":"s11"},{"stateA":"s0","label":")","stateB":"s12"},{"stateA":"s0","label":"/","stateB":"s14"},{"stateA":"s0","label":"e","stateB":"s1"},{"stateA":"s0","label":"E","stateB":"s1"},{"stateA":"s0","label":" ","stateB":"s0"},{"stateA":"s18","label":"8","stateB":"s13"},{"stateA":"s18","label":"9","stateB":"s13"},{"stateA":"s19","label":"0","stateB":"s13"},{"stateA":"s19","label":"1","stateB":"s13"},{"stateA":"s19","label":"2","stateB":"s13"},{"stateA":"s19","label":"3","stateB":"s13"},{"stateA":"s19","label":"4","stateB":"s13"},{"stateA":"s19","label":"5","stateB":"s13"},{"stateA":"s19","label":"6","stateB":"s13"},{"stateA":"s19","label":"7","stateB":"s13"},{"stateA":"s20","label":"5","stateB":"s1"},{"stateA":"s20","label":"6","stateB":"s1"},{"stateA":"s20","label":"7","stateB":"s1"},{"stateA":"s20","label":"8","stateB":"s1"},{"stateA":"s2","label":"0","stateB":"s1"},{"stateA":"s2","label":"1","stateB":"s1"},{"stateA":"s2","label":"2","stateB":"s1"},{"stateA":"s2","label":"3","stateB":"s1"},{"stateA":"s2","label":"4","stateB":"s1"},{"stateA":"s2","label":"5","stateB":"s1"},{"stateA":"s2","label":"6","stateB":"s1"},{"stateA":"s2","label":"7","stateB":"s1"},{"stateA":"s2","label":"8","stateB":"s1"},{"stateA":"s2","label":"9","stateB":"s1"},{"stateA":"s15","label":"0","stateB":"s16"},{"stateA":"s15","label":"1","stateB":"s21"},{"stateA":"s15","label":"2","stateB":"s22"},{"stateA":"s4","label":"0","stateB":"s1"},{"stateA":"s4","label":"1","stateB":"s1"},{"stateA":"s4","label":"2","stateB":"s1"},{"stateA":"s4","label":"3","stateB":"s1"},{"stateA":"s4","label":"4","stateB":"s1"},{"stateA":"s4","label":"5","stateB":"s1"},{"stateA":"s4","label":"6","stateB":"s1"},{"stateA":"s4","label":"7","stateB":"s1"},{"stateA":"s4","label":"8","stateB":"s1"},{"stateA":"s4","label":"9","stateB":"s1"},{"stateA":"s5","label":"0","stateB":"s1"},{"stateA":"s5","label":"7","stateB":"s1"},{"stateA":"s5","label":"8","stateB":"s1"},{"stateA":"s5","label":"9","stateB":"s1"},{"stateA":"s16","label":"0","stateB":"s1"},{"stateA":"s16","label":"2","stateB":"s1"},{"stateA":"s16","label":"3","stateB":"s1"},{"stateA":"s16","label":"4","stateB":"s1"},{"stateA":"s16","label":"5","stateB":"s1"},{"stateA":"s16","label":"6","stateB":"s1"},{"stateA":"s16","label":"7","stateB":"s1"},{"stateA":"s16","label":"8","stateB":"s1"},{"stateA":"s16","label":"9","stateB":"s1"},{"stateA":"s21","label":"0","stateB":"s1"},{"stateA":"s21","label":"1","stateB":"s1"},{"stateA":"s21","label":"2","stateB":"s1"},{"stateA":"s21","label":"3","stateB":"s1"},{"stateA":"s21","label":"4","stateB":"s1"},{"stateA":"s21","label":"5","stateB":"s1"},{"stateA":"s21","label":"6","stateB":"s1"},{"stateA":"s21","label":"7","stateB":"s1"},{"stateA":"s21","label":"8","stateB":"s1"},{"stateA":"s21","label":"9","stateB":"s1"},{"stateA":"s22","label":"0","stateB":"s1"},{"stateA":"s22","label":"1","stateB":"s1"},{"stateA":"s22","label":"2","stateB":"s1"},{"stateA":"s3","label":"4","stateB":"s23"},{"stateA":"s3","label":"5","stateB":"s25"},{"stateA":"s3","label":"+","stateB":"s17"},{"stateA":"s3","label":"-","stateB":"s17"},{"stateA":"s3","label":" ","stateB":"s0"},{"stateA":"s23","label":"8","stateB":"s26"},{"stateA":"s23","label":"9","stateB":"s26"},{"stateA":"s25","label":"0","stateB":"s26"},{"stateA":"s25","label":"1","stateB":"s26"},{"stateA":"s25","label":"2","stateB":"s26"},{"stateA":"s25","label":"3","stateB":"s26"},{"stateA":"s25","label":"4","stateB":"s26"},{"stateA":"s25","label":"5","stateB":"s26"},{"stateA":"s25","label":"6","stateB":"s26"},{"stateA":"s25","label":"7","stateB":"s26"},{"stateA":"s17","label":"4","stateB":"s23"},{"stateA":"s17","label":"5","stateB":"s25"},{"stateA":"s17","label":" ","stateB":"s0"},{"stateA":"s27","label":"4","stateB":"s23"},{"stateA":"s27","label":"5","stateB":"s25"},{"stateA":"s27","label":" ","stateB":"s0"},{"stateA":"s28","label":"4","stateB":"s23"},{"stateA":"s28","label":"5","stateB":"s25"},{"stateA":"s28","label":" ","stateB":"s0"},{"stateA":"s28","label":"+","stateB":"s29"},{"stateA":"s28","label":"-","stateB":"s29"},{"stateA":"s29","label":"4","stateB":"s23"},{"stateA":"s29","label":"5","stateB":"s25"},{"stateA":"s29","label":" ","stateB":"s0"},{"stateA":"s6","label":"4","stateB":"s18"},{"stateA":"s6","label":"5","stateB":"s19"},{"stateA":"s6","label":" ","stateB":"s0"},{"stateA":"s7","label":"4","stateB":"s18"},{"stateA":"s7","label":"5","stateB":"s19"},{"stateA":"s7","label":" ","stateB":"s0"},{"stateA":"s8","label":" ","stateB":"s0"},{"stateA":"s9","label":" ","stateB":"s0"},{"stateA":"s10","label":" ","stateB":"s0"},{"stateA":"s11","label":" ","stateB":"s0"},{"stateA":"s12","label":" ","stateB":"s0"},{"stateA":"s13","label":"4","stateB":"s18"},{"stateA":"s13","label":"5","stateB":"s19"},{"stateA":"s13","label":"e","stateB":"s3"},{"stateA":"s13","label":"E","stateB":"s3"},{"stateA":"s13","label":" ","stateB":"s0"},{"stateA":"s13","label":".","stateB":"s27"},{"stateA":"s14","label":" ","stateB":"s0"},{"stateA":"s14","label":"/","stateB":"s30"},{"stateA":"s30","label":" ","stateB":"s30"},{"stateA":"s1","label":"4","stateB":"s31"},{"stateA":"s1","label":"5","stateB":"s33"},{"stateA":"s1","label":" ","stateB":"s0"},{"stateA":"s31","label":"8","stateB":"s1"},{"stateA":"s31","label":"9","stateB":"s1"},{"stateA":"s33","label":"0","stateB":"s1"},{"stateA":"s33","label":"1","stateB":"s1"},{"stateA":"s33","label":"2","stateB":"s1"},{"stateA":"s33","label":"3","stateB":"s1"},{"stateA":"s33","label":"4","stateB":"s1"},{"stateA":"s33","label":"5","stateB":"s1"},{"stateA":"s33","label":"6","stateB":"s1"},{"stateA":"s33","label":"7","stateB":"s1"},{"stateA":"s26","label":"4","stateB":"s23"},{"stateA":"s26","label":"5","stateB":"s25"},{"stateA":"s26","label":" ","stateB":"s0"},{"stateA":"s26","label":"e","stateB":"s28"},{"stateA":"s26","label":"E","stateB":"s28"}],"bulkTests":{"accept":"","reject":""}}
```
# Caso de Prueba Utilizado
Este debe estar almacenado en un archivo de texto llamado "prueba.txt".

```cpp
  inFile.open("prueba.txt"); //Abrir archivo.
  
  //Almacenar lineas del archivo:
  while (!inFile.eof()) { //Mientras el archivo no se acabe:

    getline(inFile, linea); //Se obtiene una de sus lineas.
    lineas.push_back(linea); //Se guarda en el vector.
  }
  inFile.close(); //Cerrar archivo.
``` 
Contenido del archivo:
``` bash
b = 7

a = 32.4 * ( -8.6 - b ) / 6.1E-8

d = a ^ b // Esto es un comentario
```

# Resultados Obtenidos
``` bash
Token                                                                           Tipo

b                                                                               Variable 
=                                                                               Asignacion 
7                                                                               Entero 
a                                                                               Variable 
=                                                                               Asignacion 
32.4                                                                            Real 
*                                                                               Multiplicacion 
(                                                                               Parentesis que abre 
-8.6                                                                            Real 
-                                                                               Resta 
b                                                                               Variable 
)                                                                               Parentesis que cierra 
/                                                                               Division 
6.1E-8                                                                          Real 
d                                                                               Variable 
=                                                                               Asignacion 
a                                                                               Variable 
^                                                                               Potencia 
b                                                                               Variable 
// Esto es un comentario                                                        Comentario 
```



