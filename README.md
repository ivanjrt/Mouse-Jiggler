# Mouse-Jiggler - The mouse will click as it moves
``` Javascript
$cSource = @'
using System;
using System.Drawing;
using System.Runtime.InteropServices;
using System.Windows.Forms;
public class Clicker
{
//https://msdn.microsoft.com/en-us/library/windows/desktop/ms646270(v=vs.85).aspx
[StructLayout(LayoutKind.Sequential)]
struct INPUT
{ 
    public int        type; // 0 = INPUT_MOUSE,
                            // 1 = INPUT_KEYBOARD
                            // 2 = INPUT_HARDWARE
    public MOUSEINPUT mi;
}

//https://msdn.microsoft.com/en-us/library/windows/desktop/ms646273(v=vs.85).aspx
[StructLayout(LayoutKind.Sequential)]
struct MOUSEINPUT
{
    public int    dx ;
    public int    dy ;
    public int    mouseData ;
    public int    dwFlags;
    public int    time;
    public IntPtr dwExtraInfo;
}

//This covers most use cases although complex mice may have additional buttons
//There are additional constants you can use for those cases, see the msdn page
const int MOUSEEVENTF_MOVED      = 0x0001 ;
const int MOUSEEVENTF_LEFTDOWN   = 0x0002 ;
const int MOUSEEVENTF_LEFTUP     = 0x0004 ;
const int MOUSEEVENTF_RIGHTDOWN  = 0x0008 ;
const int MOUSEEVENTF_RIGHTUP    = 0x0010 ;
const int MOUSEEVENTF_MIDDLEDOWN = 0x0020 ;
const int MOUSEEVENTF_MIDDLEUP   = 0x0040 ;
const int MOUSEEVENTF_WHEEL      = 0x0080 ;
const int MOUSEEVENTF_XDOWN      = 0x0100 ;
const int MOUSEEVENTF_XUP        = 0x0200 ;
const int MOUSEEVENTF_ABSOLUTE   = 0x8000 ;

const int screen_length = 0x10000 ;

//https://msdn.microsoft.com/en-us/library/windows/desktop/ms646310(v=vs.85).aspx
[System.Runtime.InteropServices.DllImport("user32.dll")]
extern static uint SendInput(uint nInputs, INPUT[] pInputs, int cbSize);

public static void LeftClickAtPoint(int x, int y)
{
    //Move the mouse
    INPUT[] input = new INPUT[3];
    input[0].mi.dx = x*(65535/System.Windows.Forms.Screen.PrimaryScreen.Bounds.Width);
    input[0].mi.dy = y*(65535/System.Windows.Forms.Screen.PrimaryScreen.Bounds.Height);
    input[0].mi.dwFlags = MOUSEEVENTF_MOVED | MOUSEEVENTF_ABSOLUTE;
    //Left mouse button down
    input[1].mi.dwFlags = MOUSEEVENTF_LEFTDOWN;
    //Left mouse button up
    input[2].mi.dwFlags = MOUSEEVENTF_LEFTUP;
    SendInput(3, input, Marshal.SizeOf(input[0]));
}
}
'@
$TypeDef = Add-Type -TypeDefinition $cSource -ReferencedAssemblies System.Windows.Forms,System.Drawing


$left = 1 #1050
while($true) {
    [Clicker]::LeftClickAtPoint($left,900) #If want the mouse to be still, set $left to specific number
    sleep -Seconds 3
    if($Host.UI.RawUI.KeyAvailable -and ("e" -eq $Host.UI.RawUI.ReadKey("IncludeKeyup,NoEcho").Character)) {
        break;
    }
       
    $left++
    Write-Output "This is Row: $left"
    if ($left -eq 3425){$left = 1}
}
```

# Scroll-Lock Jiggler -it will press the Scroll Key -Only Works in Consoles not ISE
``` Ruby
Clear-Host
$WShell = New-Object -com "Wscript.Shell"
$stopwatch = $null

try {
    $stopwatch = [system.diagnostics.stopwatch]::StartNew()
} catch {
   Write-Host "Couldn't start the stopwatch."
}
Write-Output "Start Scrolling time: $(Get-Date -Format "dddd MM/dd hh:mm:ss tt (K)") UTC"

#This doens't work in the ISE console
$notpressed = $true
while($notpressed){
    if([console]::KeyAvailable){
        $notpressed = $false    
    }    
    else{
        Write-Host ":)" -ForegroundColor red
        $WShell.sendkeys("{SCROLLLOCK}")
        Write-Output "Elapse Time yet: " $stopwatch.Elapsed.ToString('dd\.hh\:mm\:ss')
        Write-Output "-Press ANY keys to exit-"
        Start-Sleep -Seconds 10 #Change this to setup a different loop interval
    }
}
$stopwatch.Stop() 
Write-Host -ForegroundColor Cyan "I was Scrolling for Total of: " $stopwatch.Elapsed.ToString('dd\.hh\:mm\:ss')
```

