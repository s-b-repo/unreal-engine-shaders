# unreal-engine-shaders

Two small HLSL sketches that could form the pixel-shader body of a
Custom-HLSL node in an Unreal Engine Material Function.

> **Not a runnable Unreal project.** There's no `.uproject`, no
> `Content/` folder, no `.usf` (Unreal Shader File) header — these
> are HLSL fragments, not something the UE shader compiler will pick
> up as-is.

## Files

- `distoreted reality` — sin/cos UV distortion sampled from a texture
  named `Texture` with the built-in `DefaultSampler`. About 25 lines.
  (Filename typo preserved: "distoreted" = distorted.)
- `ran seed` — hash-of-UV random-color pixel shader with a seed
  uniform. Followed by a small `FGlobalShader` C++ scaffolding block
  that references types (`FShaderResource`, `SetGlobalShader`, etc.)
  that don't line up with UE's real API — that portion won't compile
  and can be ignored when reading the HLSL.

## Using either shader inside UE

The pixel-shader bodies are the salvageable part. To try one:

1. In a UE Material, add a **Custom** node.
2. Paste the body of `distoreted reality` (or the `PS_RandomShader`
   function from `ran seed`) into the Code field.
3. Add input pins for `UV` (TEXCOORD0) and, for the distortion shader,
   a texture parameter you sample with `Texture2DSample(...)` instead
   of the raw `Texture.Sample(DefaultSampler, ...)` (Unreal's Custom
   node uses its own sampling macros).
4. Wire the result into the Material's `Emissive Color` or `Base Color`
   pin.

For a full-fat post-process material, wrap the same code inside
`PPScreenPosToViewportUV(GetSceneTextureUV(Parameters))` in place of
`UV`.

## What's *wrong* in `ran seed`'s C++ scaffolding

The `FShader_RandomShader : public FGlobalShader { … }` block at the
bottom of the file references `FShaderResource`, `ShaderSource`,
`GetGlobalShaderMap`, and `SetGlobalShader` in ways that don't match
real UE headers. It reads like an AI-generated template that never
compiled. Strip that block if you're adapting the file.

## License

MIT — see [LICENSE](LICENSE).

The original file description said "all i ask is credit you can use
all you want" — that's covered by MIT's attribution requirement.
