# DigitalCat

Certainly! Here's an example of a simple Win32 desktop pet application where the window is made transparent, and a cat image is displayed. This example uses the Win32 API to create a transparent window and display an image.

### Steps:
1. **Load a cat image**: You need a bitmap image of a cat. Save it as "cat.bmp" in the same directory as your code.
2. **Create a window with transparency**: Use the `WS_EX_LAYERED` style to create a transparent window.

Here's the complete code:

```cpp
#include <windows.h>

// Global variables
const char g_szClassName[] = "myWindowClass";
HBITMAP hBitmap;

// Step 4: the Window Procedure
LRESULT CALLBACK WndProc(HWND hwnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
    switch (msg)
    {
    case WM_CLOSE:
        DestroyWindow(hwnd);
        break;
    case WM_DESTROY:
        PostQuitMessage(0);
        break;
    case WM_PAINT:
    {
        PAINTSTRUCT ps;
        HDC hdc = BeginPaint(hwnd, &ps);
        HDC hdcMem = CreateCompatibleDC(hdc);
        HBITMAP hbmOld = (HBITMAP)SelectObject(hdcMem, hBitmap);

        BITMAP bm;
        GetObject(hBitmap, sizeof(bm), &bm);
        BitBlt(hdc, 0, 0, bm.bmWidth, bm.bmHeight, hdcMem, 0, 0, SRCCOPY);

        SelectObject(hdcMem, hbmOld);
        DeleteDC(hdcMem);
        EndPaint(hwnd, &ps);
    }
    break;
    default:
        return DefWindowProc(hwnd, msg, wParam, lParam);
    }
    return 0;
}

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow)
{
    WNDCLASSEX wc;
    HWND hwnd;
    MSG Msg;

    // Load the cat bitmap
    hBitmap = (HBITMAP)LoadImage(NULL, "cat.bmp", IMAGE_BITMAP, 0, 0, LR_LOADFROMFILE);
    if (hBitmap == NULL)
    {
        MessageBox(NULL, "Could not load cat.bmp!", "Error", MB_ICONEXCLAMATION | MB_OK);
        return 0;
    }

    // Step 1: Registering the Window Class
    wc.cbSize        = sizeof(WNDCLASSEX);
    wc.style         = 0;
    wc.lpfnWndProc   = WndProc;
    wc.cbClsExtra    = 0;
    wc.cbWndExtra    = 0;
    wc.hInstance     = hInstance;
    wc.hIcon         = LoadIcon(NULL, IDI_APPLICATION);
    wc.hCursor       = LoadCursor(NULL, IDC_ARROW);
    wc.hbrBackground = (HBRUSH)(COLOR_WINDOW+1);
    wc.lpszMenuName  = NULL;
    wc.lpszClassName = g_szClassName;
    wc.hIconSm       = LoadIcon(NULL, IDI_APPLICATION);

    if(!RegisterClassEx(&wc))
    {
        MessageBox(NULL, "Window Registration Failed!", "Error!", MB_ICONEXCLAMATION | MB_OK);
        return 0;
    }

    // Step 2: Creating the Window with WS_EX_LAYERED for transparency
    hwnd = CreateWindowEx(
        WS_EX_LAYERED,
        g_szClassName,
        "Cat Desktop Pet",
        WS_POPUP,
        CW_USEDEFAULT, CW_USEDEFAULT, 300, 300,
        NULL, NULL, hInstance, NULL);

    if(hwnd == NULL)
    {
        MessageBox(NULL, "Window Creation Failed!", "Error!", MB_ICONEXCLAMATION | MB_OK);
        return 0;
    }

    // Make the window transparent
    SetLayeredWindowAttributes(hwnd, RGB(255, 255, 255), 0, LWA_COLORKEY);

    ShowWindow(hwnd, nCmdShow);
    UpdateWindow(hwnd);

    // Step 3: The Message Loop
    while(GetMessage(&Msg, NULL, 0, 0) > 0)
    {
        TranslateMessage(&Msg);
        DispatchMessage(&Msg);
    }

    // Cleanup
    DeleteObject(hBitmap);

    return Msg.wParam;
}
```

### Explanation:
1. **Load the Bitmap**: The `LoadImage` function loads the cat bitmap image from a file.
2. **Register Window Class**: The window class is registered with the `WNDCLASSEX` structure.
3. **Create Window with Transparency**: The `WS_EX_LAYERED` style is used to allow transparency. The `SetLayeredWindowAttributes` function sets the transparency color key to white (`RGB(255, 255, 255)`), making white pixels transparent.
4. **Paint the Window**: The `WM_PAINT` message is handled to draw the cat image onto the window.

### Compile and Run:
To compile and run this program, you'll need a C++ development environment that supports Win32 programming, such as Microsoft Visual Studio. Save the bitmap as "cat.bmp" in the same directory as your code file.
