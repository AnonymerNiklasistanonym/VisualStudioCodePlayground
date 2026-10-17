# VisualStudioCodePlayground

<!-- TOC -->

- [Settings](#settings)
- [Git](#git)
- [Debugging](#debugging)
- [Workspaces (.code-workspace)](#workspaces-code-workspace)
- [Chat](#chat)
	- [OpenAPI URL (e.g. llama.cpp)](#openapi-url-eg-llamacpp)

<!-- /TOC -->

## Settings

Settings are stored in 3 locations:

1. *User Settings* (`~/.config/Code/User/settings.json`)

   - Global, applies to all projects

2. *Settings* (`.vscode/settings.json`)

   - Applies to the current project
   - Take precedence over *User Settings*

3. *Workspace Settings* (`your.code-workspace`)

   - When a workspace was created and then opened the settings take precedence over all existing settings/debug configurations/tasks

## Git

The default diff view shows the whole file meaning if its big it is really hard to tell where changes were made.
Luckily there is a setting which will hide all lines that did not change and highlights the lines that were changed:

*User Settings*

```json
{
    // ...
    // Only show lines/regions that were changed and hide the other lines per default
    "diffEditor.hideUnchangedRegions.enabled": true,
}
```

## Debugging

Via the *Run and Debug* section you can configure debug sessions for multiple programming languages or setups like:

- Godot C++ extension
- Node.js
  - Local program
  - Website JS (e.g. React)
  - WebView

To create a workflow you can first define tasks like *build debug*, *install dependencies* in a `.vscode/tasks.json` and then set up the debugger/debug session in `.vscode/launch.json` which can depend on a task or run one when the debug session is exited.

TODO

## Workspaces (`.code-workspace`)

With workspaces you can manage multiple directories and override extensions or general settings.
In the following is a list of possibly useful settings when using workspaces outside of extensions:

```json
{
	// ...
	// TODO
}
```

## Chat

### OpenAPI URL (e.g. llama.cpp)

If you have a hosted instance like a local `llama.cpp` server you can connect it to the *Chat* and use it as agent:

1. *Chat: Manage Language Models*
2. *Add Models*
3. *Custom Endpoint*
4. Set a *Group Name* like *llama.cpp*
5. Either provide an API key or if its a local isntance just press `Enter` for no API key
6. In most cases *Chat Completions* should be chosen as API type

```json
[
	{
		"name": "llama.cpp",
		"vendor": "customendpoint",
		"apiType": "chat-completions",
		"models": [
			{
				"id": "unsloth/gemma-4-E4B-it-qat-GGUF:UD-Q4_K_XL",
				"name": "gemma4",
				"url": "http://127.0.0.1:8082/v1/chat/completions",
				"toolCalling": true,
				"vision": true,
				"maxInputTokens": 128000,
				"maxOutputTokens": 16000
			}
		]
	}
]
```

<details>
<summary>`llama.cpp` start script</summary>

```sh
MODEL=unsloth/gemma-4-E4B-it-qat-GGUF:UD-Q4_K_XL
# -ngl 0 => CPU
ARGUMENTS="--spec-type draft-mtp --spec-draft-n-max 3 -ngl 999 -fa on --tools exec_shell_command --reasoning on --reasoning-budget 2048 --port 8082"
LLAMACPP_DIR=~/Documents/llama.cpp

set -x

cd $LLAMACPP_DIR
git pull
cmake -B build -G Ninja -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release -DLLAMA_BUILD_TESTS=OFF && cmake --build build
$LLAMACPP_DIR/build/bin/llama-server -hf $MODEL $ARGUMENTS
```

</details>

Setting the following in settings will also hide the *Bring your own key* popup:

```json
{
    // ...
    "chat.byokUtilityModelDefault": "none"
}
```
