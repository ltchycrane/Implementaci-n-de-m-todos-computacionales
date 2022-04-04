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



