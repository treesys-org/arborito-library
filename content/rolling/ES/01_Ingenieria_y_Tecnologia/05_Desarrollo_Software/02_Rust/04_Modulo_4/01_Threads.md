@title: Threads: std::thread y Send/Sync
@icon: 🧵
@description: spawn, join, mensajes con canales.
@order: 1

# Threads: std::thread y Send/Sync

`thread::spawn` requiere `'static` por seguridad. **mpsc** para canales.

@quiz: ¿Qué bound requiere spawn sobre el closure?
@option: Copy
@correct: 'static (sin referencias a stack no static)
@option: Debug
