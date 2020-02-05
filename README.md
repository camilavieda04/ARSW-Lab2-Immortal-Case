# ARSW-Lab2-Immortal-Case

## Integrantes
- Juan David Navarro
- Jimmy Armando Chirivi
- Sarah Camila Vieda

## Parte I 

1. ¿Por qué es este consumo? ¿Cuál es la clase responsable?
	La clase responsable es la Consumer. Este consumo se debe a que la clase Consumer siempre esta preguntando si hay elementos en la cola y la clase Productor debe producir y agregar a la cola lo más eficiente posible.


2. Verifique on JVisualVM que se reduce el consumo de CPU.

	Consumo inicial sin modificaciones del programa.

	![consumo](https://user-images.githubusercontent.com/44879884/73458691-aa76be80-4343-11ea-9eff-c77be2b71d92.PNG)

	Reducción del consumo de CPU .

	Modificación del Consumidor.

	``` java
	@Override
    public void run() {
        while (true) {
            synchronized (queue) {
                while (queue.size() == 0) {
                    queue.notifyAll();
                    try {
                        queue.wait();
                    } catch (InterruptedException ex) {
                        Logger.getLogger(Consumer.class.getName()).log(Level.SEVERE, null, ex);
                    }
                }
                int elem = queue.poll();
                System.out.println("Consumer consumes " + elem);
            }
        }
    }	
	```
	Modificación del Productor.
	
	``` java
	@Override
    public void run() {
        while (true) {
            synchronized (queue) {
                dataSeed = dataSeed + rand.nextInt(100);
                System.out.println("Producer added " + dataSeed);
                queue.add(dataSeed);
                queue.notifyAll();
                try {
                    queue.wait();
                } catch (InterruptedException ex) {
                    Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                }
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException ex) {
                    Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                }
            }
        }
    }
	```

	![ConsumoOptimo](https://user-images.githubusercontent.com/44879884/73458693-aa76be80-4343-11ea-94d4-d6a7ad3d682a.PNG)

3. Haga que el productor produzca muy rápido y el consumidor consume lentamente. Verifique que al establecer un pequeño límite para el 'stock', no haya un alto consumo de CPU o errores.

	En la clase Productor la modificamos para que cada que la cola este llena con el limete stock notifique y se quede esperando a que el consumidor consuma esto hace que el productor produzca mas rapido de lo que el consumidor consuma. 
	
	``` java
	@Override
    public void run() {
        while (true) {
            synchronized (queue) {
                while(stockLimit == queue.size()){
                    queue.notifyAll();
                    try {
                        queue.wait();
                    } catch (InterruptedException ex) {
                        Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                    }
                }
                dataSeed = dataSeed + rand.nextInt(100);
                System.out.println("Producer added " + dataSeed);
                queue.add(dataSeed);
                queue.notifyAll();
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException ex) {
                    Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                }
            }
        }
    }
	```
	Ahora vemos una parte de la prueba que realizamos donde observamos que el producto produce mas rapido de lo que se consume.	![Captura](https://user-images.githubusercontent.com/44879884/73511281-22300200-43b3-11ea-91bd-a2e34aae0f02.PNG)

	El consumo de CPU se mantuvo entre los rangos anteriores como lo podemos ver en la siguente imagen:

	![Captura2](https://user-images.githubusercontent.com/44879884/73511285-25c38900-43b3-11ea-9b11-81163b9fb394.PNG)

## Parte II

1. Reglas del juego:
   - Tenemos N jugadores inmortales. 
   - Cada jugador conoce al jugador anterior (N-1).
   - Cada jugador siempre esta atacando a otro inmortal. El jugador que ataque primero le resta M puntos a su oponente y aumenta sus puntos de vida en la misma cantidad. 
   - No hay un solo ganador, lo más probable es que al final solo queden dos luchando indefinidamente eliminando y agregando puntos de vida. 
   
2. ¿Cuál debería ser el valor de la suma de los puntos de vida de todos los jugadores?
   
   El valor debería ser 100 * N cantidad de jugadores.
   
3. Ejecute la aplicación y verifique cómo funciona la opción pausar y verificar. ¿Se cumple el invariante?
   
   Ejecutamos la aplicación con 3 inmortales y al momento de ver la funcionalidad de la opción 'pausar y revisar' nos podemos dar cuenta que no se cumple el invariante ya que la suma de la vida total de todos los jugadores deberia ser en este caso de 300 y el resultado de la aplicación es 1000.
   
   ![Capture1](https://user-images.githubusercontent.com/48154086/73752599-3e270100-472f-11ea-8abe-882f51f58d80.PNG)
   
4. Realizamos la implementación del método pause and check y resume, a continuación podremos ver esta implementación:

	Pause and Check.
	``` java
		public void actionPerformed(ActionEvent e) {  
	  
	    int sum = 0;  
	    for (Immortal im : immortals) {  
	        im.parar(im);  
	        sum += im.getHealth();  
	    }  
	    statisticsLabel.setText("<html>"+immortals.toString()+"<br>Health sum:"+ sum);  
	}
	```
		
	``` java
		public void parar(Immortal i) {  
	    isRun = false;  
	}
	```
	``` java
		synchronized (immortalsPopulation){  
	    this.fight(im);  
	}
	```
	Método Resume

	``` java
		public void actionPerformed(ActionEvent e) {  
	  
	    for (Immortal im : immortals) {  
	        im.reload(im);  
	    }  
	}
	```

	``` java
		public void reload(Immortal i){  
	    isRun = true;  
	    synchronized (this){  
	        notifyAll();  
	    }  
	}
	```
5.  Verifique la operación nuevamente (haga clic en el botón muchas veces). ¿Se cumple o no la invariante?
	Si se cumple el invariante realizando la prueba con N = 10 inmortales.
	![Capture2](https://user-images.githubusercontent.com/44879884/73856278-33d63700-4803-11ea-9232-9faa9fd38462.PNG)
![Capture3](https://user-images.githubusercontent.com/44879884/73856284-359ffa80-4803-11ea-937a-330cf3692195.PNG)
![Capture1](https://user-images.githubusercontent.com/44879884/73856286-359ffa80-4803-11ea-8b47-268be8744607.PNG)
	
6. Identifica posibles regiones críticas con respecto a la lucha de los inmortales. Implemente una estrategia de bloqueo que evite las condiciones de carrera. Recuerde que si necesita usar dos o más 'bloqueos' simultáneamente, puede usar bloques sincronizados anidados

	Una de las regiones críticas que encontramos fue en el método fight() ya que todos los inmortales usan este método durante la ejecución del programa. Para solucionarlo implementamos bloques sincronizados simultáneos.

	``` java
		public void fight(Immortal i2) {  
	    synchronized (this){  
	        synchronized (i2){  
	            if (i2.getHealth() > 0 ) {  
	                i2.changeHealth(i2.getHealth() - defaultDamageValue);  
	                this.health += defaultDamageValue;  
	                updateCallback.processReport("Fight: " + this + " vs " + i2 + "\n");  
	            } else {  
	                updateCallback.processReport(this + " says:" + i2 + " is already dead!\n");  
	            }  
	        }  
	    }  
	}
	```
7. Después de implementar su estrategia, comience a ejecutar su programa y preste atención a si se detiene. Si es así, use los programas jps y jstack para identificar por qué se detuvo el programa.

	Al implementar nuestra estrategia realizamos las pruebas con diferentes N de inmortales desde los valores mas pequeños como 3 hasta el valor mas grande 10000 y el programa funcionó correctamente.
	![Capture4](https://user-images.githubusercontent.com/44879884/73857869-887ab180-4805-11ea-919a-808989473cfd.PNG)
	
8. Considere una estrategia para corregir el problema identificado anteriormente (puede revisar el Capítulo 15 de la concurrencia de Java en la práctica nuevamente).

9. Una vez que se corrige el problema, rectifique que el programa continúa funcionando constantemente cuando se ejecutan 100, 1000 o 10000 inmortales. Si en estos casos grandes el invariante comienza a ser violado nuevamente, debe analizar lo que se hizo en el paso 4.

	Prueba #1 N = 100

	![Capture5](https://user-images.githubusercontent.com/44879884/73858156-f030fc80-4805-11ea-878e-dd0f2744186b.PNG)

	Prueba #2 N = 1000

	![Capture6](https://user-images.githubusercontent.com/44879884/73858284-25d5e580-4806-11ea-8a05-9b2999931a72.PNG)

	Prueba #3 N=10000

	![Capture7](https://user-images.githubusercontent.com/44879884/73858390-50c03980-4806-11ea-8a4e-f65e0f585962.PNG)

10. elemento molesto para la simulación es que en cierto momento hay pocos 'inmortales' vivos que pelean con 'inmortales' ya muertos. Es necesario suprimir a los muertos inmortales de la simulación a medida que mueren.

	10.1   Analizando el esquema de operación de simulación, ¿podría esto crear una condición de carrera? Implemente la funcionalidad, ejecute la simulación y vea qué problema surge cuando hay muchos 'inmortales' en ella. Escriba sus conclusiones al respecto en el archivo RESPUESTAS.txt.
	    
	10.2.  Corrija el problema anterior SIN usar la sincronización, ya que hacer que el acceso a la lista compartida de inmortales sea secuencial haría la simulación extremadamente lenta.

 11. Para finalizar implemente la opción STOP.
 
```java
	JButton btnStop = new JButton("STOP");  
	btnStop.addActionListener(new ActionListener() {  
	    @Override  
	  public void actionPerformed(ActionEvent e) {  
	  
	        for (Immortal im : immortals) {  
	            im.parar(im);  
	        }  
	    }  
	});  
	  
	btnStop.setForeground(Color.RED);  
	toolBar.add(btnStop);
```
