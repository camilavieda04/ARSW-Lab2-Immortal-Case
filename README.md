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
   
   
   
 
