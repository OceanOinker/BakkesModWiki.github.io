---
title: Creating an ImageWrapper
author: Martinn
---

## Creating an ImageWrapper
* You should use some kind of reference type that manages the memory. `std::shared_ptr<ImageWrapper>` is my recommendation.
* The constructor takes 3 arguments (2 are optional)
* The optional arguments are two bools that decide if the core will preload the images for use in the canvas\imgui.
* The ImageWrapper is a resource managing class. This means that you have to keep this object "alive" as long as you want to use the image. When the object goes out of scope \ gets destructed it cleans up the resource it manages.
* I have tried to delete the constructors that could cause issues (copy constructor).
* Stick to `std::shared_ptr` and you almost can't mess up.


## Using a ImageWrapper
 1. Use the constructor with the second or third argument true (or both).
{{< highlight cpp "linenos=table" >}}
// ImageWrapper(std::string path, bool canvasLoad = false, bool ImGuiLoad = false);
myImage = std::make_shared<ImageWrapper>(gameWrapper->GetDataFolder() / "MyPluginFolder" / "MyImage.png", true, true);
{{< / highlight >}}

2. Make sure to null check.
 {{< highlight cpp "linenos=table" >}}
if (!myImage) { return; }
{{< / highlight >}}
 
3. Load the resource if you didn't use the optional args.
{{< highlight cpp "linenos=table" >}}
myImage->LoadForCanvas();
{{< /highlight >}}

4. 
    a. Pass it to the canvas wrapper in a drawable callback
{{< highlight cpp "linenos=table" >}}
canvas.DrawTexture(testImage.get(), 1); 
// there are multiple functions in the canvaswrapper that accept ImageWrapper*
{{< /highlight >}}

    b. Use it in your imgui render code
{{< highlight cpp "linenos=table" >}}
if (auto pTex = myImage->GetImguiTex()) {
    auto rect = myImage->GetSizeF();
    ImGui::Image(pTex, { rect.width, rect.height });
}
{{< /highlight >}}


PS:
The backend will load in the resource whenever you try to use the images if it hasn't already been loaded. So you actually don't have to worry about it, but the loading can cause some lag\stutters.
So it's better to do it during loading the plugin -  when such behaviour can be expected.
