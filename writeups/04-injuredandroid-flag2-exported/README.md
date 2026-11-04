# InjuredAndroid — Flag 2: una pantalla que se abre desde fuera (activity exportada)

> Parte de mi serie de writeups de pentesting. Esta flag ya no se resuelve con una contraseña, sino explotando una pantalla mal protegida.

## Contexto

Mismo montaje que la flag 1: Mac M2, emulador ARM de Android Studio, jadx y adb. App objetivo: [InjuredAndroid](https://github.com/B3nac/InjuredAndroid).

La pista en el código de esta flag decía: *"Key words Activity and exported."*

## Concepto: activities exportadas

Cada pantalla de una app es una *activity*. Normalmente es privada: solo la propia app la abre, navegando de una pantalla a otra. Pero en el manifest se puede marcar `android:exported="true"`, y entonces **cualquier otra app del teléfono puede abrirla directamente**, saltándose el flujo normal.

A veces eso es necesario (la pantalla principal que abre el sistema al tocar el ícono, por ejemplo). El problema es cuando se exporta sin querer una pantalla que debería estar protegida.

## Encontrar la pantalla

En el manifest había varias activities exportadas con nombres raros. Uno destacaba: `b25lActivity`. La parte `b25l` tenía pinta de **Base64** (una forma de representar texto, no un cifrado). La decodifiqué:

```bash
echo "b25l" | base64 -d
# → one
```

Así que la pantalla "escondida" era la "one activity": la de la flag 2.

## La explotación

Con adb le pedí a Android que abriera esa pantalla directamente:

```bash
adb shell am start -n b3nac.injuredandroid/.b25lActivity
```

Desglose del comando: `adb shell` ejecuta algo dentro del teléfono; `am` es el *activity manager* (el que abre pantallas); `start -n` indica qué activity abrir; y lo último es la dirección (paquete `/` activity).

El emulador saltó directo a la pantalla de la flag, sin pasar por el flujo normal de la app. Eso es justo lo que haría una app maliciosa instalada en el mismo teléfono.

**Flag:** `S3c0nd_F1ag`

## La vulnerabilidad

Un componente exportado sin protección. Una app de banco con una pantalla así podría dejar que otra app abriera la vista de saldos sin login.

Como desarrollador, la lección es directa: cada `android:exported="true"` en el manifest es una puerta abierta al resto del sistema, y hay que preguntarse si esa pantalla de verdad debe estar abierta.

## Cómo se corrige

- Exportar solo lo que de verdad necesita ser abierto desde fuera; el resto, `exported="false"`.
- Si una pantalla exportada maneja datos sensibles, validar permisos antes de mostrar nada.

## Lo que aprendí

- Reconocer Base64 a simple vista (texto de letras/números, a veces termina en `=`) y decodificarlo con `base64 -d`.
- Una activity exportada se puede invocar desde fuera con adb, saltándose el flujo de la app.
- Encadenar técnicas: decodificar un nombre para descubrir la pantalla, y luego abrirla con adb.
