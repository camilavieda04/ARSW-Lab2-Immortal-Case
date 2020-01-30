# ARSW-Lab2-Immortal-Case

## Integrantes
- Juan David Navarro
- Jimmy Armando Chirivi
- Sarah Camila Vieda

## Parte I 

1. ¿Por qué es este consumo? ¿Cuál es la clase responsable?
	La clase responsable es la Consumer. Este consumo se debe a que la clase Consumer siempre esta preguntando si hay elementos en la cola y la clase Productor debe producir y agregar a la cola lo más eficiente posible.


3. Verifique on JVisualVM que se reduce el consumo de CPU.

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

3. Haga que el productor consuma muy rápido y el consumidor consume lentamente. Verifique que al establecer un pequeño límite para el 'stock', no haya un alto consumo de CPU o errores.