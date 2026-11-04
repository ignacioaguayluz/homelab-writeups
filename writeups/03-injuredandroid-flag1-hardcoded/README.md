# InjuredAndroid — Flag 1: un secreto escrito en el código (hardcoded secret)

> Parte de mi serie de writeups de pentesting. Soy desarrollador mobile y aprendo a atacar apps para construirlas mejor. Cuento también lo que no entendía y cómo lo resolví.

## El montaje

Para pentesting mobile no uso Kali: trabajo directo en mi Mac (M2, 8 GB), porque el emulador de Android corre imágenes ARM nativas y va rápido.

- **Android Studio** con un emulador (Pixel con imagen de sistema **ARM64**, no x86).
- **jadx** para leer el código de un APK: `brew install jadx`.
- **adb** (viene con Android Studio) para hablarle al emulador por terminal.

adb no estaba en el PATH. Lo agregué una sola vez:

```bash
echo 'export PATH="$PATH:$HOME/Library/Android/sdk/platform-tools"' >> ~/.zshrc
source ~/.zshrc
```

**App objetivo:** [InjuredAndroid](https://github.com/B3nac/InjuredAndroid), una app vulnerable a propósito con retos tipo CTF. Descargué solo el APK desde Releases (el resto del repo trae las soluciones).

## Concepto base: qué es un APK

Un APK es un archivo comprimido (como un .zip) que contiene el código compilado de la app, sus recursos (textos, imágenes) y el **AndroidManifest.xml**, la ficha técnica que declara todas las pantallas (*activities*). "Compilado" significa traducido a un formato para la máquina, no para humanos. **jadx** lo traduce de vuelta a Java legible. Estudiar la app así, sin ejecutarla, es *análisis estático*.

## El reto

Abrí el APK con jadx y entré a la pantalla de la primera flag, `FlagOneLoginActivity`. En su método de envío encontré esto:

```java
public final void submitFlag(View view) {
    EditText editText = (EditText) findViewById(R.id.editText2);
    if (d.s.d.g.a(editText.getText().toString(), "F1ag_0n3")) {
        // pantalla de éxito
    }
}
```

La app compara lo que escribo contra el texto `"F1ag_0n3"`, que está escrito ahí mismo. Al principio busqué `.equals` y no encontré nada: como el código viene de Kotlin, la comparación quedó como `d.s.d.g.a(a, b)`, que significa lo mismo ("¿a es igual a b?") pero con otro nombre.

**Flag:** `F1ag_0n3`

## La vulnerabilidad

Un *hardcoded secret*: un valor sensible escrito directo en el código. Es de los errores más comunes en mobile: tokens, claves de API y contraseñas que se dejan en la app creyendo que, por estar compilada, nadie los verá. Con jadx se leen en segundos.

Esto me toca de cerca: en React Native pasa igual, una API key en el código viaja dentro del bundle del APK y se puede extraer.

## Cómo se corrige

- No guardar secretos en el código de la app. Lo que deba ser secreto vive en el servidor, y la app lo pide cuando lo necesita.
- Asumir que todo lo que se empaqueta en el APK **lo puede leer cualquiera**.

## Lo que aprendí

- Un APK se puede abrir y leer; "compilado" no es lo mismo que "protegido".
- Cómo leer código descompilado que viene de Kotlin, donde los nombres quedan cortos (`d.s.d.g.a`).
