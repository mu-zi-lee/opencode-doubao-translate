# Magpie Doubao Translate

[简体中文](README.md) | [English](README.en.md)

Translate text directly using your own Doubao cookies or cookies you are authorized to use. No doubao-translate2api service, Docker deployment, or additional API key is required.

## Disclaimer

This project is an independent, unofficial implementation. It is not affiliated with or partnered with Doubao, Magpie, or other related platforms, and does not imply their authorization, approval, or endorsement. All relevant names and trademarks belong to their respective owners.

This project is intended for learning, research, and development discussions. Users are responsible for checking and complying with applicable laws, platform terms of service, and account usage rules. Use only accounts and cookies you own or are legally authorized to use. Do not use this project for unlawful activities or to infringe on others' rights.

Cookies are sensitive login credentials. Keep them secure, do not publish or commit them to a repository, and do not share a `plugin-auth.json` file containing cookies. Text submitted for translation is sent to Doubao and its related translation services. Before submitting content, confirm that you have the right to process it and assess any privacy or confidentiality requirements.

The software is provided "as is" under the [MIT License](LICENSE), without guarantees of continued API availability, translation accuracy, account security, or suitability for a particular purpose. Users must assess and accept the risks of web API changes, account restrictions or bans, cookie exposure, service interruptions, translation errors, and other consequences of use. Verify translations of important content.

To the extent permitted by applicable law, the authors and contributors are not liable for losses, claims, or other liabilities arising from using or being unable to use this project. Read this disclaimer and the [MIT License](LICENSE) before downloading, installing, or using the software. Do not use it if you cannot accept these risks.

## Compliance Risks

This plugin uses cookies to make automated requests to Doubao's web translation API, rather than an official developer API. The project has not obtained or verified specific platform authorization for this access method and does not guarantee compliance with the platform's current terms of service. The points below identify risks to assess; they do not assert that the platform expressly prohibits every such request or determine the legality of any specific use.

- **Account authorization is not authorization to use the API in this way.** Using your own account or cookies with the account holder's permission does not establish that the platform permits automated access, bulk requests, wrapping its web service as an API, or providing access to third parties. Check the platform's current rules on automated access, credential sharing, and redistribution of its services before use.
- **Personal use, learning, and research do not automatically remove responsibility.** Free, open-source, low-volume, or personal use does not guarantee compliance with platform terms or applicable law. If the access method is not permitted, consequences may include access restrictions, account suspension, service termination, and, depending on the circumstances, contractual disputes or other liabilities.
- **Do not circumvent platform restrictions.** Do not use this project to bypass CAPTCHAs, access controls, payment requirements, rate limits, or account sanctions, or to use cookies obtained without authorization. Stop the relevant requests if the platform denies access or requests that they cease.
- **Confirm your rights to process submitted text.** Source text is sent to Doubao and its related translation services. For personal data belonging to others, customer records, company secrets, or copyrighted content, confirm the necessary rights to process and submit it and assess applicable privacy, confidentiality, copyright, and data transfer requirements. Importing cookies into a remote Magpie instance also requires appropriate authorization to store those credentials.
- **Assess commercial use and third-party services separately.** Before using the plugin in a paid service, business workflow, or translation service offered to others, check whether the platform permits that purpose and assess authorization, data processing, and contractual requirements. The MIT License grants rights to this project's code only; it does not grant rights to Doubao's services, accounts, APIs, or related content.

The project's disclaimer and MIT License do not replace platform authorization or remove responsibilities imposed by applicable law. For uses with strict compliance requirements, choose an official API or service that explicitly authorizes the intended purpose and obtain the permissions required for your specific use.

## Project Links

| Link | Purpose |
| --- | --- |
| [Magpie direct plugin](https://github.com/mu-zi-lee/magpie-doubao-translate) | This plugin's distribution repository; install directly from a local or remote Magpie instance |
| [API service source and deployment guide](https://github.com/mu-zi-lee/doubao-translate2api) | Deploy on a NAS or server to provide a compatible API for multiple translation clients |
| [Docker Hub image](https://hub.docker.com/r/muzileee/doubao-translate2api) | Pull `muzileee/doubao-translate2api` to deploy the API service |

The service and plugin share the same translation core. Install this plugin when you only need translation in Magpie. Use the API service or Docker image when you need an admin interface, a cookie pool, API keys, or support for multiple clients.

## Direct Installation (Including Remote Instances)

Enter the following in the installation field at the bottom of Magpie's Plugins > Discover page:

```text
github:mu-zi-lee/magpie-doubao-translate
```

Click Install, then open Installed or Providers and select **Import Doubao Cookie** under **Doubao Translation** to sign in.
You can also use the CLI on the machine running Magpie:

```sh
magpie plugin add github:mu-zi-lee/magpie-doubao-translate
magpie plugin login doubao-translate
```

The GitHub distribution repository includes the built plugin and its runtime dependencies. No Node.js development environment, Docker, or additional build steps are required.
To pin a version, use `github:mu-zi-lee/magpie-doubao-translate#v0.1.0`.
Update the plugin in Magpie or run `magpie plugin update`.

## Build from the Local Main Project

Run the following in the main project's directory:

```sh
npm ci
npm run build:plugin
magpie plugin add ./magpie-plugin
magpie plugin login doubao-translate
```

Alternatively, enter the absolute path to the `magpie-plugin` folder in Magpie's Plugins > Add Plugin page.
Build the folder first. The plugin package includes its runtime dependencies and has no installation scripts.

During sign-in, paste a complete Cookie header or browser-exported cookie JSON. You may also enter an account name.
The cookies must include `sessionid`, `sid_tt`, and `uid_tt`. The plugin contacts Doubao during sign-in to validate them.
Each sign-in can add one account; Magpie handles scheduling and failover.
Credentials are stored in `plugin-auth.json` in Magpie's configuration directory with permissions set to 600. This file contains normalized cookies and account names; do not share it.
The plugin uses a custom import flow and saves cookies after successful validation. No additional API key or browser-based sign-in is needed.
Expired cookies must be imported again. Automatic sign-in and renewal are not supported.

## Models and Translation

| Model | Engine |
| --- | --- |
| `doubao-translate/doubao-ai` | Doubao AI translation |
| `doubao-translate/volcengine-translate` | Volcengine translation |
| `doubao-translate/microsoft-translator` | Microsoft Translator |

All three engines are accessed through Doubao's web API. The provider uses Chat Completions, and Magpie handles client protocol conversion.
The default target language is Simplified Chinese. A request's `target_lang`, `X-Doubao-Target-Lang`, or explicit translation instruction takes precedence over the plugin default.
Only the last user message's text is translated. Conversation memory, images, tools, reasoning, and structured output are not supported.
Select one of the models above explicitly in your translation client. Avoid using them for general chat, coding, or automatically routed general-purpose tasks.
The models' 32000-token input/output budgets are conservative gateway settings, not upstream quotas or exact character limits.

Long text is split automatically, with the original order and line breaks restored. Results are returned only after every segment succeeds.
Streaming requests emit SSE after the complete translation succeeds; translation is not streamed token by token.
Token usage is reported as 0. No verifiable upstream plan quota is available, and the plugin does not report fabricated usage.
The plugin does not save source text, translated text, or separate statistics files.

## Options

Set these values in the plugin's `options` configuration in Magpie, or use the CLI:

```sh
magpie plugin options opencode-doubao-translate '{"targetLang":"en","maxConcurrency":4}'
```

| Option | Default | Description |
| --- | --- | --- |
| `targetLang` | `"zh"` | Default target language |
| `scene` | `2` | Doubao translation scene, 1-6 |
| `maxConcurrency` | `4` | Total upstream concurrency for the plugin and the recommended per-account limit in Magpie, 1-1000 |
| `requestTimeoutMs` | `45000` | Timeout for a single upstream request |
| `totalTimeoutMs` | `180000` | Overall timeout, including queueing, segmentation, and retries |
| `authTimeoutMs` | `10000` | Sign-in validation timeout |
| `maxRetries` | `0` | Retries for temporary failures per batch, 0-10; failover is handled by Magpie by default |
| `queueMax` | `100` | Maximum number of requests waiting in the plugin's internal queue |
| `queueTimeoutMs` | `30000` | Queue timeout |

Disable and re-enable the plugin after making changes to reload the provider options.
The plugin does not read the standalone service's `.env`, cookie files, or account pool.
An expired Doubao session returns `X-Magpie-Sign-In: expired`. HTTP 429 remains 429; other failures use a compatible API error format.
Temporary network or service failures do not mark a session as expired. Proxy settings come from Magpie's account/provider proxy configuration.

## Testing and Packaging

```sh
magpie provider test doubao-translate doubao-ai
```

This command checks connectivity. Translation acceptance checks should also explicitly send source text and a target language.
For development checks, run `npm run typecheck`, `npm test`, and `npm run build` in the main project.
Local mock tests and Bun loading checks do not constitute acceptance testing with a real Magpie instance, cookies, or Doubao translation.
Checks using the official Magpie Bun plugin host and a simulated upstream have covered loading, saving cookies during sign-in, and streaming responses. Real Doubao translation and remote clients still require acceptance testing.
The plugin bundles Zod; its MIT license is included in `THIRD_PARTY_LICENSES.txt`.

After building, you can create a standalone npm package without running installation scripts:

```sh
npm pack ./magpie-plugin
```

The plugin is currently installed through its GitHub distribution repository. It has not been published to npm or the recommended plugin marketplace.
