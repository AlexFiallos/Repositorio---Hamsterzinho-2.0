# Repositorio---Hamsterzinho-2.0
Código Fuente - Readme
README – FASE 5: Software embebido (estado, lógica y seguridad)

1. Descripción general
El presente proyecto implementa el software embebido de un sistema automatizado de dispensación de alimento y agua controlado mediante un microcontrolador ESP32. El software ha sido diseñado priorizando la seguridad, la recuperación ante fallos y un comportamiento predecible, mediante el uso de una máquina de estados finitos (FSM), interlocks de protección y mecanismos básicos de supervisión.

2. Máquina de estados finitos (FSM)
La lógica de control del sistema se basa en una máquina de estados finitos que regula el ciclo completo de operación. Los estados definidos son los siguientes:

- STAND BY:
  Estado de reposo por defecto. El sistema permanece encendido, con el LED Power activo, sin actuadores en funcionamiento y con monitoreo constante de sensores.

- DETECCIÓN DE PRESENCIA:
  Se valida la presencia del animal. En este estado se verifica que existan condiciones seguras para operar, tales como disponibilidad de agua y alimento, y ausencia de bloqueos activos.

- DISPENSADO / BOMBEO:
  Se activan los actuadores correspondientes (servomotor de dispensación y bomba de agua). Durante este estado se supervisan el tiempo de activación, el nivel de agua y el comportamiento del sistema.

- VENTANA DE CONSUMO:
  Los actuadores se desactivan y se concede un tiempo para que el animal consuma el alimento y el agua dispensados. Los sensores continúan activos para validar si hubo consumo real.

- DESCARTE:
  Si no se detecta consumo válido durante la ventana de consumo, el sistema descarta la ración sobrante y evita una nueva dispensación inmediata.

- PURGA / LIMPIEZA:
  Se realiza una activación breve de las bombas para purgar el sistema y evitar acumulación de residuos o agua estancada.

- RETORNO A STAND BY:
  El sistema reinicia contadores y banderas internas y vuelve al estado de reposo, quedando listo para un nuevo ciclo.

- ERROR / BLOQUEO:
  Estado seguro al que se ingresa ante la detección de una condición anómala. En este estado se desactivan todos los actuadores y se mantiene la señalización de error hasta que la condición sea corregida.

Las transiciones entre estados se realizan en función de eventos como detección de presencia, validación de sensores, cumplimiento de tiempos máximos y detección de fallos.

3. Interlocks de seguridad
El sistema incorpora los siguientes interlocks para prevenir daños y condiciones de operación inseguras:

- Interlock por nivel bajo de agua:
  Si el sensor de nivel detecta ausencia de agua, se bloquea la activación de la bomba y de la electroválvula. El sistema entra en estado de error y activa el LED correspondiente.

- Interlock por tiempo máximo de activación:
  Cada vez que un actuador es encendido, se inicia un temporizador interno. Si el tiempo máximo permitido es superado, el actuador se apaga automáticamente y el sistema entra en estado de bloqueo.

- Interlock por falla de sensor:
  El sistema valida rangos y coherencia temporal de las lecturas. Lecturas imposibles o inconsistentes provocan la invalidación del sensor y el bloqueo de la acción asociada.

4. Watchdog lógico
El software implementa un watchdog lógico que supervisa la ejecución del ciclo principal del programa. En caso de detectar bloqueo o falta de respuesta del sistema, se realiza un reinicio controlado, retornando al estado STAND BY con los actuadores desactivados y conservando la señalización de error si corresponde.

5. Recuperación segura ante fallos
Ante cualquier condición anómala (nivel bajo de agua, timeout, falla de sensor o falta de alimento), el sistema:
- Desactiva inmediatamente los actuadores.
- Entra en un estado seguro de error o bloqueo.
- Señaliza visualmente la condición detectada.
- Permite la recuperación únicamente cuando la condición ha sido corregida o mediante reinicio controlado.

6. Indicadores LED
El sistema utiliza LEDs como interfaz básica de diagnóstico:

- LED Power:
  Permanece siempre encendido mientras el sistema esté energizado, indicando funcionamiento básico del microcontrolador.

- LED Dispensando:
  Indica que el sistema se encuentra en el estado de dispensado de alimento.

- LED Bomba:
  Indica que la bomba de agua se encuentra activada.

- LED Sin agua:
  Indica nivel bajo de agua o bloqueo por falta de suministro.

- LED Sin comida:
  Indica que no se alcanza el peso objetivo o que el depósito de alimento está vacío.

7. Logs básicos
El sistema genera registros básicos a través del puerto serial, los cuales permiten observar:
- Estados activos de la FSM.
- Eventos de error o bloqueo.
- Activación y desactivación de actuadores.
Estos logs facilitan el diagnóstico durante pruebas y validación del sistema.
