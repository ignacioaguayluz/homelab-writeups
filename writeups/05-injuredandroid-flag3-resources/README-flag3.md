# InjuredAndroid — Flag 3: un secreto en los recursos (seguir la referencia)

> Parte de mi serie de writeups de pentesting. Esta flag es parecida a la primera, pero el secreto ya no está en el código: está en los recursos, y el código solo apunta hacia él.

## Contexto

Mismo montaje que las flags anteriores: Mac M2, emulador ARM de Android Studio, jadx y adb. App objetivo: [InjuredAndroid](https://github.com/B3nac/InjuredAndroid).

## Concepto: código que apunta a un recurso

Una app Android no guarda todos sus textos en el código. Los textos visibles viven en un archivo de recursos llamado **strings.xml**, y el código los pide por su nombre con `getString(...)`. Es una práctica normal y correcta (sirve, por ejemplo, para traducir la app). El problema es cuando en esos recursos se guarda algo que debería ser secreto.

## Siguiendo el hilo

Abrí `FlagThreeActivity` en jadx y miré su método de envío:

```java
public final void submitFlag(View view) {
    EditText editText = (EditText) findViewById(R.id.editText2);
    if (d.s.d.g.a(editText.getText().toString(), getString(R.string.cmVzb3VyY2VzX3lv))) {
        // pantalla de éxito
    }
}
```

La diferencia con la flag 1 está aquí: en la flag 1 el valor correcto era un texto escrito directo (`"F1ag_0n3"`). En esta, en cambio, no hay texto; hay `getString(R.string.cmVzb3VyY2VzX3lv)`, que significa "trae el string que se llama `cmVzb3VyY2VzX3lv` desde los recursos". El código no guarda la respuesta, solo guarda **dónde está**.

Ese nombre de recurso, `cmVzb3VyY2VzX3lv`, parece Base64. Decodificado da `resources_yo`, que es una guiñada: la pista te manda a los recursos. Las pistas de la propia pantalla lo confirman: *"R stands for resources"* y *"Check .xml files."*

Fui al recurso en `res/values/strings.xml`:

```xml
<string name="cmVzb3VyY2VzX3lv">F1ag_thr33</string>
```

**Flag:** `F1ag_thr33`

## La vulnerabilidad

Es el mismo error que la flag 1 (un secreto empaquetado en la app), pero en otro sitio. Para un atacante da igual que el valor esté en el código o en `strings.xml`: las dos cosas viajan dentro del APK y se leen sin esfuerzo. Mover un secreto a los recursos no lo protege.

La habilidad que practiqué aquí es **seguir una referencia**: saltar del código al recurso al que apunta. En apps reales los secretos muchas veces no están escritos en el código, sino en recursos o archivos de configuración a los que el código solo apunta.

## Cómo se corrige

- Lo mismo que en la flag 1: un secreto no debe empaquetarse en la app, ni en el código ni en los recursos. Vive en el servidor y la app lo pide cuando lo necesita.

## Lo que aprendí

- Que `getString(R.string.X)` manda a buscar el valor al recurso llamado `X`, y hay que seguir ese salto.
- Que un nombre de recurso también puede estar codificado (Base64) y dar una pista.
