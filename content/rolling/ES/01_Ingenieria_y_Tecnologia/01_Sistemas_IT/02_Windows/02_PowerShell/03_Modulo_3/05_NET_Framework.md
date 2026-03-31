@title: Interacción con .NET Framework
@icon: 🔗
@description: Desbloqueando todo el poder de la plataforma .NET desde PowerShell.
@order: 5

# Uso de .NET desde PowerShell

PowerShell está construido sobre **.NET**. Puedes instanciar tipos con **`New-Object`**, llamar métodos estáticos con **`[Type]::Method()`** y usar **ensamblados** cargados con `Add-Type`.

@section: Tipos y constructores

```powershell
$sb = [System.Text.StringBuilder]::new()
$sb.AppendLine('hola') | Out-Null
$sb.ToString()
```

@section: New-Object

```powershell
New-Object -TypeName System.Diagnostics.Stopwatch
```

@section: Add-Type

Para C# embebido o P/Invoke (APIs Win32):

```powershell
Add-Type @"
public class MathUtil {
  public static int Square(int x) { return x * x; }
}
"@
[MathUtil]::Square(7)
```

**Cuidado:** compilar en cada ejecución puede ser lento; en módulos, compila una vez.

@section: Fechas y cultura

```powershell
[System.DateTime]::Parse('2026-03-29')
Get-Culture
```

@section: Interop administrativa

Muchos cmdlets ya envuelven .NET (`Get-Process` → `System.Diagnostics.Process`). Si falta un cmdlet, la clase .NET suele existir.

@section: Práctica

1.  Usa `[System.Net.Dns]::GetHostEntry('localhost')`.
2.  Explica cuándo preferirías un cmdlet frente a .NET directo (legibilidad, soporte).

@quiz: ¿Qué sintaxis llama un método estático de una clase .NET?
@option: .method()
@correct: [ClassName]::Method()
@option: static.method()

@quiz: ¿Qué cmdlet permite incrustar código C# en una sesión de PowerShell?
@option: Import-CSharp
@correct: Add-Type
@option: New-Class
