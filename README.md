# ToonShader_ShaderGraph-VertexShader

Abstract
-------------------------------------------
En este proyecto se van a llevar a cabo las habilidades obtenidas durante el transcurso del semestre tales como la animación de vértices para simular un efecto de agua, un modelo de luz custom para la ambientación de la luz y el vertexshading para simular el movimiento de la vegetación con viento, todo esto en una misma escena para ejercitar los conocimientos adquiridos y mediante la investigación fortalecerlos y pulirlos.


Introducción 
---------------------------------------------
En este proyecto se realizara un escenario con Unity, mediante diferentes tipos de shading aprendidos a lo largo del semestre, utilizando los siguientes puntos a tener en cuenta:

- Vegetacion, con vertex shading (incorporar simulacion de viento)
- Modelo de luz custom
- Modelo de Agua, con vertex shading


Desarrollo
----------------------------------------------
El proyecto consta de 3 materiales principales: Flower, Water, y Toon Shader, los cuales se explicarán más a detalle a continuación.

**Flower**

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%201.png)

Para realizar el shader del zacate o flor, se realizaron varios pasos
Inicialmente, se necesitarán las siguientes variables:
Albedo: que será la textura base 
Wind Speed: Que manejara la velocidad del viento
Wind intensity: Para modificar la intensidad del viento
Noise Density: Que manejara que tanto se deformara la textura
Normal Intensity: Para regular la textura normal.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%202.png)

Una de las cosas más importantes son la textura original y la textura de normales, que se consiguieron de esta manera: Un Sample Texture 2D que reciba la textura del zacate o flor, y su salida RGBA(4) este conectada con el Base Color (3) de Fragment. Su canal A(1) estará conectado con Alpha (1). Mientras que la textura normal se conseguirá utilizando el.

nodo Normal from texture, que recibe la textura original y la variable Normal Strength para manejar la intensidad de la textura. Esta se conecta con Normal (Tangent Space)(3)

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%203.png)

Sin embargo, esto resulta en un zacate estático, cuando lo que se necesita es un shader que simule el viento. 

Lo primero que se necesita para hacer esto, es algo que maneje el tiempo y la velocidad del viento, por lo que se usa Tiling and Offset, donde sus entradas son las de Position y un producto de la variable Wind Speed con el tiempo.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%204.png)

Después, la salida de ese Tiling and Offset se pasa a un Gradient Noise, donde la escala está conectada con la variable de Noise Density, esta pasa a un Subtract con un valor X de 0.5, y se multiplica con la variable de Wind Intensity.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%205.png)

Para el siguiente paso, necesitamos la posición del modelo, y se separan los ejes X, Y y Z. El eje X pasa a un Add, donde se suma con el ruido creado anteriormente, que es lo que causa que se mueva el shader en eje X, mientras que el eje Y y el eje Z se mantienen igual. Al final, se combinan de nuevo los 3 ejes.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%206.png)

Después de esto, el resultado de combinar los ejes pasa a un Lerp, que tiene como entradas la posición del modelo, y la variable Y del UV. Luego se pasa a un Transform de tipo World, y la salida de esto se conecta con el nodo Position (3) de Vertex.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%207.png)

**Water**

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%208.png)

Para el agua se necesitarán las siguientes variables: 
Depth, que se mantendrá en 1
Depth Strength, que controla la profundidad del agua
Clear Water, que es el color del agua más cercano a la tierra
Dark Water, es el color del resto del agua
Normal 1 y Normal 2, que serán las texturas normales para las olas
Wave Strength, para regular la fuerza de las olas
Displacement, para manejar que tanto sube y baja la marea 
Smooth, que controla el brillo de la luz en las olas

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%209.png)

Después de crear las variables, se realizará la parte de profundidad, la cual consta de varios nodos. Se tomará el Alpha del nodo Screen Position, y esta se sumará con la variable Depth. Después, se realizará una resta entre esto y el producto de Scene Depth con la variable Farplane (1) del nodo Camera.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2010.png)

Después de este paso, se multiplicará el resultado de lo obtenido en Depth con la variable Depth Strength, y esto se juntara con un Clamp.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2011.png)

Ahora se realizará la flexión del color de agua, la cual consta de un Lerp que recibe lo obtenido en Black Water Foam, y los colores Dark Water y Clear Water. El resultado de esto pasa a ser Base Color(3) de nuestro Fragment, y el Alpha se obtiene por medio del nodo Split.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2012.png)

Esta parte del shader manejara las normales y como estas se mueven. Primero es necesario pasar el tiempo a 2 nodos Divide, uno tendrá un valor B de 50, y el otro de -25. Estos se conectaran con nodos Tiling and Offset, los cuales se juntaran con las 2 texturas normales y luego se sumarán.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2013.png)

El siguiente paso es crear la fuerza del normal de las olas, esto se consigue con un nodo Lerp, que toma a la variable Wave Strength y Black Foam Force. Esto pasa al nodo Normal Strength, donde se combina con la textura normal obtenida anteriormente.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2014.png)

El resultado de esto pasa a Normal (Tangent Space) de nuestro fragmento, y la variable Smooth, pasa a Smoothness.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2015.png)

Para iniciar con la parte de Vertex del shader, se realiza lo siguiente: Se toma el tiempo, este pasa a un nodo Divide, con un valor B(1) de 100, y esto pasa a un nodo Tiling and Offset, con Tiling (1,1)

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2016.png)

Para finalizar, se realizará la parte de ruido, que principalmente nos sirve para mover la posición de las vértices del shader, para simular olas. Para esto se toma el resultado del nodo anterior, y se junta con un nodo Gradient Noise con una escala de 20, este se multiplica con la variable Displacement. Luego se combinan el producto anterior, con los ejes X y Z de la posición de las olas. El resultado de esto pasa a Position (3) de Vertex.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2017.png)

Toon Shading
Para el Toon Shading se necesitaron los siguientes subgrafos y archivos de hlsl:
Dabs, que sirve para dar un efecto de líneas blancas a nuestro Toon Shading.
Direct Specular, la cual recibe un specular, smoothness, una dirección, color, y que sirve para darle cierta luz de reflexión al shader.
Un sistema de luz Lambert, el cual nos sirve para darle caída a la sombra en nuestro modelo.
Light Atten Color, nodo que utilizaremos más adelante.
Main light, la cual servirá para mandarle una dirección de luz.
NDotL el cual servirá para darle intensidad a la luz.
Ramp, el cual creará un efecto de sombra que le dará el toque “toon” al modelo.
Side Light, el cual dibujaba o seguía la silueta del modelo desde donde se apunta la luz.
 
Para el efecto, constó de varias cosas, comenzando por una, para generar el efecto Ramp, se une el NDotL a nuestro subgrafo de Ramp el cual contiene una Light Intensity, después este se conecta desde hard y soft ramp al nodo llamado Soft Ramp, este finalmente se une a un Add junto con el Lambert.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2018.png)

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2019.png)

Para el Specular, lo primero que se hizo fue unir la dirección del MainLight a la dirección del DirectSpecular, de igual manera, el node de Specular Color y Smoothness se conectan a Specular y Smoothness del DirectSpecular, finalmente el subgrafo de Light Atten Color se conecta al Color del DirectSpecular.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2020.png)

**Light Atten**
![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2021.png)

El Albedo se realiza uniendo el Main Texture a un Sample Texture 2D, el cual luego se multiplicará por el nodo Albedo.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2022.png)

Después de esto, tomaremos el Subgrafo de Dabs y lo multiplicaremos con la salida del Specular, el cual luego se sumará con la última suma realizada en Ramp.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2023.png)

**Dabs**
![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2024.png)

Después de haber hecho todo esto, se realizó el Final Effect, el cual consta de multiplicar la última multiplicación que salió del Albedo con la última suma realizada del Ramp con la multiplicación de los Dabs con el Specular.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2025.png)

Finalmente, sumaremos el último multiply con el subgrafo de SideLight para así finalmente colocarlo en el Base Color.

![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2026.png)

**Side Light**
![alt text](https://github.com/Lelmats/ToonShader_ShaderGraph-VertexShader/blob/main/imagenes/Foto%2027.png)


Conclusión
-------------------------------------------------
Para finalizar, podríamos decir que los resultados fueron satisfactorios, ya que, se logro crear con exito lo que se planteo, sobre todo si hablamos del agua y la vegetación ambas creadas con Vertex Shading. Los efectos logrados cumplieron su fución con éxito, todo esto creado con texturas, normal maps, modelos, etc. Representando de esta menera, de una forma realista los efectos de viento, agua, y en el caso del Toon Shading logrando crear el efecto con éxito

Referencia
-------------------------------------------------
**Water Shader**
- Ilett, D. (2020, 5 abril). Stylised Water in Shader Graph and URP. Daniel Ilett: Games | Shaders | Tutorials. https://danielilett.com/2020-04-05-tut5-3-urp-stylised-water/
- Protokoll Studio. (2020, 11 agosto). Unity water with shader graph. https://area51.protokoll-studio.com/unity-water-with-shader-graph-tutorial/
- Corvo Studio. (s. f.). HDRP & URP water shader. Corvostudio Documentation. Recuperado 21 de mayo de 2021, de https://corvostudio.it/docs.php?sw=hdrp_urp_water_shader-guide
- Chen, T. (2021, 5 febrero). Introducing shader graph’s new master stack. Unity. https://blog.unity.com/technology/introducing-shader-graphs-new-master-stack
- Shader Graph Low Poly Water. (2021, 6 marzo). Game Developers Hub. https://game-developers.org/shader-graph-low-poly-water/
- Cartoon-style water wave effect | Unity-Shader Graph. (s. f.). Programmer Sought. Recuperado 21 de mayo de 2021, de https://www.programmersought.com/article/82094205593/
- Novel Tech. (2021, 6 enero). CREATING a SIMPLE WATER SHADER USING SHADERGRAPH FOR UNITY. https://www.noveltech.dev/unity-simple-water-shader-shadergraph/
- O’Reilly, J. (2018, 5 octubre). Art That Moves: Creating Animated Materials with Shader Graph. Unity. https://blog.unity.com/technology/art-that-moves-creating-animated-materials-with-shader-graph
- Creating water with shader graph in unity| 2D shader basics. (2020, 27 octubre). CGTubes. https://www.cgtutes.com/water-shader-graph-unity/
- Tuttle, J. (2019, 1 noviembre). Unity Water Shaders - Jason Tuttle. Medium. https://medium.com/@JasonTuttle/unity-water-shaders-2b615e696adc

**Vertex Shading**
- Technologies, U. (2021, 15 mayo). Unity - Manual: Vertex and fragment shader examples for the Built-in Render Pipeline. Unity. https://docs.unity3d.com/Manual/SL-VertexFragmentShaderExamples.html
- Bellucci, F. (2018, 1 octubre). Vertex Shader Tutorial for Unity. Febucci. https://www.febucci.com/2018/10/vertex-shader/
- Zucconi, A. (2020, 29 julio). Vertex and fragment shaders in Unity3D. Alan Zucconi. https://www.alanzucconi.com/2015/07/01/vertex-and-fragment-shaders-in-unity3d/
- Vertex Displacement. (2018, 16 junio). Ronja’s Tutorials. https://www.ronja-tutorials.com/post/015-wobble-displacement/
- Vertex Shader - OpenGL Wiki. (2017, November 10). OpenGL Wiki. https://www.khronos.org/opengl/wiki/Vertex_Shader
- Marinacci, J. (2019, February 28). Customizing Vertex shaders. Medium. https://medium.com/@joshmarinacci/customizing-vertex-shaders-86527c5693b2
- Ureña, C. (n.d.). Evaluación del MIL mediante programación del cauce gráfico con GLSL. Universidad de Granada. Retrieved May 23, 2021, from https://lsi2.ugr.es/%7Ecurena/doce/vr/pracs.ext/05/
- Rost, R. (2006, January 25). Vertex Shader | A Simple Shading Example in OpenGL Shading Language. Informit. https://www.informit.com/articles/article.aspx?p=446452&seqNum=2
- Vertex Shaders | ISF Documentation. (n.d.). ISF Documentation. Retrieved May 23, 2021, from https://docs.isf.video/primer_chapter_5.html#vertex-shaders-vs-fragment-shaders
- Vertex shader. (2011, September 25). Learn OpenGL ES. https://www.learnopengles.com/tag/vertex-shader/

**Modelo de Luz**
- Technologies, U. (s. f.). Tipos de luz - Unity Manual. Unity. Recuperado 21 de mayo de 2021, de https://docs.unity3d.com/es/2018.4/Manual/Lighting.html

- Technologies, U. (2015, 15 mayo). Unity - Manual: Custom lighting models in Surface Shaders. Unity. https://docs.unity3d.com/Manual/SL-SurfaceShaderLighting.html

- Flick, J. (2019, 30 noviembre). Directional Lights. Catlike Coding. https://catlikecoding.com/unity/tutorials/custom-srp/directional-lights/

- Technologies, U. (s. f.). Unity - Manual: Custom Lighting models in Surface Shaders. Unity. https://dev.rbcafe.com/unity/unity-5.3.3/en/Manual/SL-SurfaceShaderLighting.html

- Hedquist, A. (2018, 23 enero). Toon shader with a custom lighting model - Aaron Hedquist. Medium. https://medium.com/@aarhed/toon-shader-with-a-custom-lighting-model-276030c0338b

- Unity Toon Shader Tutorial at Roystan. (s. f.). Roystan. Recuperado 21 de mayo de 2021, de https://roystan.net/articles/toon-shader.html

- Single Step Toon Light. (2018, 20 octubre). Ronja’s Tutorials. https://www.ronja-tutorials.com/post/031-single-step-toon/

- Lindman, A. (2019, 31 junio). Custom lighting in shader graph: Expanding your graphs in 2019. Unity. https://blog.unity.com/technology/custom-lighting-in-shader-graph-expanding-your-graphs-in-2019

- Technologies, U. (2015, 15 mayo). Unity - Manual: Surface Shader lighting examples. Unity. https://docs.unity3d.com/Manual/SL-SurfaceShaderLightingExamples.html

- Kalupahana, D. (2019, 20 junio). Shaders in Unity — Lambert and Ambient - Deshan Kalupahana. Medium. https://medium.com/@deshankalupahana/shaders-in-unity-lambert-and-ambient-ceba9fab6cfa

