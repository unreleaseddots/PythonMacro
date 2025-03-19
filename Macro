import os
import time
import threading
import ctypes
import keyboard

# SendInput configuration (ultra-fast clicking)
SendInput = ctypes.windll.user32.SendInput

# INPUT structure for mouse events
class MouseInput(ctypes.Structure):
    _fields_ = [("dx", ctypes.c_long),
                ("dy", ctypes.c_long),
                ("mouseData", ctypes.c_ulong),
                ("dwFlags", ctypes.c_ulong),
                ("time", ctypes.c_ulong),
                ("dwExtraInfo", ctypes.POINTER(ctypes.c_ulong))]

class Input(ctypes.Structure):
    _fields_ = [("type", ctypes.c_ulong),
                ("mi", MouseInput)]

# Mouse event definitions
INPUT_MOUSE = 0
MOUSEEVENTF_LEFTDOWN = 0x0002
MOUSEEVENTF_LEFTUP = 0x0004

# Control variables
click_rate = 34
macro_running = threading.Event()  # Replaces boolean variable
thread_macro = None

# Simulate an ultra-fast mouse click
click_down = Input(type=INPUT_MOUSE, mi=MouseInput(dwFlags=MOUSEEVENTF_LEFTDOWN))
click_up = Input(type=INPUT_MOUSE, mi=MouseInput(dwFlags=MOUSEEVENTF_LEFTUP))

def click_mouse():
    SendInput(1, ctypes.byref(click_down), ctypes.sizeof(click_down))
    SendInput(1, ctypes.byref(click_up), ctypes.sizeof(click_up))

# Highly optimized macro loop
def click_loop():
    global click_rate
    interval_ns = int(1e9 / click_rate)  # Time between clicks in nanoseconds
    next_click = time.perf_counter_ns()

    while macro_running.is_set():
        click_mouse()
        next_click += interval_ns
        
        while time.perf_counter_ns() < next_click:
            ctypes.windll.kernel32.Sleep(0)  # Releases CPU time without delaying

# Start the macro
def start_macro():
    global thread_macro
    if macro_running.is_set():
        print("Macro is already running.")
        return

    print("✅ Macro Started!")
    macro_running.set()
    thread_macro = threading.Thread(target=click_loop, daemon=True)
    thread_macro.start()

# Stop the macro
def stop_macro():
    if not macro_running.is_set():
        print("Macro is not running.")
        return

    print("🛑 Macro Stopped!")
    macro_running.clear()
    if thread_macro and thread_macro.is_alive():
        thread_macro.join()

# Increase click rate
def increase_click_rate():
    global click_rate
    click_rate += 1
    print(f"🔥 Click rate increased to {click_rate} CPS")

# Decrease click rate
def decrease_click_rate():
    global click_rate
    if click_rate > 1:
        click_rate -= 1
        print(f"🐢 Click rate decreased to {click_rate} CPS")

# Display the menu
def show_menu():
    print("\n--- Command Menu ---")
    print("F9: Start Macro")
    print("F8: Stop Macro")
    print("F7: Increase Click Rate")
    print("F6: Decrease Click Rate")
    print("Esc: Exit Program")
    print("--------------------")

# Instantly exit the program
def exit_program():
    print("❌ Exiting program...")
    stop_macro()
    os._exit(0)

# Keyboard shortcuts
keyboard.add_hotkey('F9', start_macro)
keyboard.add_hotkey('F8', stop_macro)
keyboard.add_hotkey('F7', increase_click_rate)
keyboard.add_hotkey('F6', decrease_click_rate)
keyboard.add_hotkey('Esc', exit_program)

# Display the menu
show_menu()

# Keep the program running until ESC is pressed
keyboard.wait('Esc')
