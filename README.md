# homelab-writeups

Writeups de mi aprendizaje en pentesting, documentados reto por reto. Soy desarrollador mobile y estudio seguridad para construir mejores apps: entender cómo se atacan es la mejor forma de aprender a protegerlas.

Cada writeup explica el concepto detrás de la vulnerabilidad, el paso a paso de cómo la resolví, **cómo se corrige**, y lo que no entendía al empezar. La idea no es solo mostrar la solución, sino el razonamiento.

## Entorno

- **Web:** Kali Linux en una VM (UTM) sobre un Mac M2, con red aislada y objetivos en Docker.
- **Mobile:** emulador de Android (ARM) en el Mac, con jadx y adb para análisis estático y dinámico.

## Writeups

| #   | Reto                                                                   | Tema             | Vulnerabilidad             |
| --- | ---------------------------------------------------------------------- | ---------------- | -------------------------- |
| 01  | [Montaje del homelab](writeups/01-homelab-setup/)                      | Infraestructura  | —                          |
| 02  | [Juice Shop — Score Board](writeups/02-juice-shop-score-board/)        | Web              | Security through obscurity |
| 03  | [InjuredAndroid — Flag 1](writeups/03-injuredandroid-flag1-hardcoded/) | Mobile / Android | Hardcoded secret           |
| 04  | [InjuredAndroid — Flag 2](writeups/04-injuredandroid-flag2-exported/)  | Mobile / Android | Activity exportada         |

## Sobre mí

Estudiante de Ingeniería de Software en Costa Rica, enfocado en desarrollo mobile. Aprendo haciendo, y este repo es parte de ese proceso.

> Todo el trabajo aquí se hace en entornos propios o en plataformas que lo autorizan explícitamente, con fines educativos.

