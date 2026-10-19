Los escáneres de seguridad como Nessus, Acunetix o el escáner activo de Burp Suite funcionan buscando patrones técnicos conocidos: prueban firmas, inyectan payloads predefinidos y verifican configuraciones de cabeceras o versiones desactualizadas. 

Su gran limitación y "punto ciego" aparece frente a las **fallas de lógica de negocio**, porque en estos casos no hay un error técnico en el código. No hay una cookie mal configurada, no salta un error 500, ni se rompe una consulta en la base de datos. El software hace exactamente lo que el desarrollador programó, pero el flujo está mal diseñado desde su arquitectura o concepción.

Un escáner automatizado no comprende el contexto del negocio ni sabe qué se supone que deba pasar. No tiene criterio para evaluar si una máquina de estados es coherente o si un usuario debería poder saltarse el paso de pago en un carrito de compras, cambiar cantidades a números negativos o canjear un cupón repetidas veces.

## Por qué la auditoría manual es indispensable?

La auditoría manual es insustituible porque solo un analista humano puede comprender el propósito de la aplicación, el objetivo del cu/negocio/endpoint,el contexto de la transacción y la intención de cada funcionalidad:

 Un auditor puede razonar cómo violar las reglas del sistema (por ejemplo, alterar el orden de los pasos de un formulario). Un humano entiende el impacto real que tiene una incoherencia lógica sobre la empresa (fraude financiero, alteración de inventario o bypass de privilegios), algo que una herramienta automatizada jamás podrá interpretar por sí sola. Por esto es indispensable. (Hasta que estas herramientas comienzen a emplear IA, cuando esto suceda se comenzaran a plantear estas cosas y la auditoria manual pierda fuerzas)

