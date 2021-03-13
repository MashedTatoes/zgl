# ZGL-glad – Zig OpenGL Bindings

Fork of [zgl](https://github.com/ziglibs/zgl) targeting `GLAD` instead of `libepoxy` 

Im building this with my own personal project so bugs and discrepancies between libepoxy and GLAD will only be fixed if I encounter them. 


## Example

```zig
//Getting a window open with GLFW and initialzing OpenGL 
if(c.glfwInit() == GL_FALSE){
  std.debug.print("Error init GLFW",.{});
}
const window: ?*c.GLFWwindow = c.glfwCreateWindow(1024,780,"Hello world!",null,null);
c.glfwMakeContextCurrent(window);
if(gl.gladLoadGL(@ptrCast(gl.gladLoadProc,c.glfwGetProcAddress)) == false){
  std.debug.print("Failed to load GLAD\n", .{});
}

//Example of a build.zig to get OpenGL up and running along with GLFW on Windows

pub fn build(b: *std.build.Builder) void {
  const target = b.standardTargetOptions(.{});
  const mode = b.standardReleaseOptions();
  const exe = b.addExecutable("main", "src/main.zig");
  
  //C Source files and Includes
  exe.addIncludeDir("deps/glfw/include");
  exe.addIncludeDir("deps/glad/include");
  exe.addCSourceFile("deps/glad/src/glad.c",&[_][]const u8 {});
  exe.addPackagePath("gl","deps/zgl-glad/zgl.zig");
  
  //Libaries
  exe.addLibPath("deps/glfw/src/Release");
  exe.linkSystemLibrary("glfw3");
  exe.linkSystemLibrary("c");
  exe.linkSystemLibrary("opengl32");
  exe.linkSystemLibrary("user32");
  exe.linkSystemLibrary("gdi32");
  exe.linkSystemLibrary("shell32");
 
  exe.setTarget(target);
  exe.setBuildMode(mode);
    
  exe.install();

  const run_cmd = exe.run();
  run_cmd.step.dependOn(b.getInstallStep());
  if (b.args) |args| {
      run_cmd.addArgs(args);
  }

    
  const run_step = b.step("run", "Run the app");
  run_step.dependOn(&run_cmd.step);
 
}
```

## Development Philosophy

This library is developed incrementally. That means that functions and other things will be included on-demand and not just for the sake of completeness.

If you think a function is missing, fork the library, implement the missing function similar to the other functions and make a pull request. Issues that request implementation of missing functions will be closed immediatly.
