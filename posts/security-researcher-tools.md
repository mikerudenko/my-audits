# My Personal Security Researcher Toolbox

In the world of smart contract auditing and blockchain security, having a well-organized and efficient toolkit is essential. The right tools can significantly speed up your auditing process and help uncover potential vulnerabilities that might otherwise go unnoticed. Here's a rundown of my personal toolkit, organized by category, that I find invaluable in my work.

## Base Development Setup
Having a solid base setup is crucial as it ensures a smooth workflow during the auditing process.
- **MacOS (Unix-like systems)**: Many auditor tools are UNIX-compatible, which makes Linux-like systems an easy choice for installation.
- **VS Code**: It stands out due to its extensive range of plugins compared to other IDEs.
- **Notion**: A fantastic task management tool that helps in organizing thoughts and tasks effectively.

## VS Extensions
Visual Studio Code extensions enhance the functionality of VS Code, providing additional tools and features for smart contract development and auditing.

- Solidity Language & Themes. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-solidity-language)
 Solidity syntax highlighter that can easily detect state transitions and also showcase some possible vulnerabilities like unchecked returns.
 Install from VS Marketplace, and it will automatically highlight syntax and show potential issues in your Solidity code.

- Solidity Metrics. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-metrics)
 Created by [@ConsensysAudits](https://twitter.com/ConsensysAudits), shows useful stats like contract dependencies, Source Lines of Code (SLOC), and displays stats in charts.
 Install the extension, open a Solidity file, and access the metrics through the command palette.

- Solidity Visual Developer. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
 Create UML, graphviz chart for smart contract, making it easier to understand functions flow and intercommunication in the protocol between smart contracts.
 Install the extension, open a Solidity file, and use the command palette to generate diagrams.

- Test Explorer UI. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-test-explorer)
 Visualize smart contract test coverage in code.
 Install the extension, run your tests, and view the coverage in the Test Explorer UI.

- Tools for Solidity. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=AckeeBlockchain.tools-for-solidity)
 Enhance navigation within Solidity contracts with features like Go to Type Definition, Go to Implementation, and Find References.
 Install the extension and use the added navigation features while editing Solidity files.

- Ethereum Security Bundle. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=tintinweb.ethereum-security-bundle)
 Bundles Solidity language syntax, Yul, and inline bookmarks which are super useful for setting bookmarks in the audit process and writing comments.
 Install the bundle, and utilize the features while auditing Solidity contracts.

- ETHover. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-ethover)
 Work easily with remote Ethereum addresses, hover over them, download their bytecode, and more.
 Install the extension, hover over Ethereum addresses in your code to view additional information.

- Foundry Test Explorer. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=naps62.foundry-vscode-test-adapter)
 Visual UI for running foundry tests in VS Code.
 Install the extension, and run your Foundry tests through the visual UI.

- Even Better TOML. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml)
 Since Foundry reads config from .toml files, this extension is needed.
 Install the extension to better handle .toml files in your workspace.

- GitHub Copilot. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
 AI pair programmer powered by OpenAI’s GPT-3.5.
 Install the extension, and use it to get code suggestions as you type.

- GitHub linker. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=gimenete.github-linker)
 Right-click on a file or a line in code to copy a link to GitHub, useful for writing audit reports.
 Install the extension, right-click on a file or code line to copy the GitHub link.

- Solidity Debugger. [VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=hosho.solidity-debugger)
 Run and debug Solidity transactions right in VS Code.
 Install the extension, set breakpoints in your Solidity code, and run/debug transactions.


## AI Tools
AI tools offer a layer of automation and intelligence that could be invaluable in code analysis and smart contract auditing.
- **OpenAI Chat**: [Link](https://chat.openai.com/)
  - **Purpose**: General AI tool for various purposes.
  - **How to use**: Navigate to the website and follow the instructions to utilize the AI.
- **Etherscan Code Reader**: [Link](https://etherscan.io/code-reader)
  - **Purpose**: GPT-3 integrated in Etherscan to help analyze smart contracts.
  - **How to use**: Access through the Etherscan website and input the smart contract code for analysis.
- **AuditWizard**: [Link](https://www.auditwizard.io/)
  - **Purpose**: Similar to Etherscan Code Reader for analyzing smart contracts.
  - **How to use**: Visit the website, and follow the instructions to analyze your smart contract code.


## Code Specific Tools
Code-specific tools offer a range of functionalities that aid in auditing and analyzing smart contracts in a more detailed manner. The following tools are instrumental for static analysis, mutation testing, and various other forms of testing and analysis that help in identifying vulnerabilities in smart contracts:
- [RareSkills CLI tools](https://www.rareskills.io/post/smart-contract-audit-tools): A collection of CLI tools categorized into Static Analysis, Mutation Testing, Fuzzing and Invariant Testing, Formal Verification and Symbolic Execution, and Visualization Tools.
- [4naly3er](https://github.com/Picodes/4naly3er): A tool for gas usage analysis and optimization.
- [sol2uml](https://github.com/naddison36/sol2uml): CLI tool to create UML diagrams from Solidity code, aiding in understanding the structure and relationships within the code.
- [pyrometer](https://github.com/nascentxyz/pyrometer): Examines function inputs and the range of possible input values, useful for checking smart contract constraints and invariants.
- [solhint](https://github.com/protofire/solhint): Another Solidity linter to help enforce coding style and best practices.
- [slitherin](https://github.com/pessimistic-io/slitherin): An update for the Slither tool with more code vulnerability detection methods.
- [Contract Diff Comparison Tool](https://x48.tools/diff): Useful for comparing contract versions.
- [dethcode](https://github.com/dethcrypto/dethcode): Inspect deployed contracts in a browser VS Code version.

## Transactions Debugging Tool
These tools are crucial for tracing and debugging transactions on the blockchain, which is fundamental in auditing smart contracts:
- [Tenderly Dashboard](https://dashboard.tenderly.co/)
- [Openchain Trace](https://openchain.xyz/trace)
- [Phalcon Explorer](https://explorer.phalcon.xyz/)
- [EthTx Info](https://ethtx.info/)
- [MetaSleuth](https://metasleuth.io/): Inspect transaction flow to monitor market movements, track fund flows of criminal activities, and DYOR to avoid scams.

## Contract Decoder Tools
Decoding tools are essential for understanding contract data, especially when dealing with complex or minified code:
- [Online ABI Decoder](https://gnidan.github.io/abi-to-sol/)
- [Unverified ABI Code Getter](https://abi.w1nt3r.xyz/)
- [Calldata Decoder](https://calldata-decoder.apoorv.xyz/)
- [ByteGraph](https://bytegraph.xyz/): View any contract as a graph of opcodes.
- [Online Bytecode Decompiler](https://library.dedaub.com/decompile)
- [Sourcify Playground](https://playground.sourcify.dev/): Retrieve contract metadata.
- [Calldata Decoder](https://tools.deth.net/calldata-decoder)

## Contract Storage Readers
Understanding how data is stored and accessed is crucial in auditing smart contracts for potential vulnerabilities:
- [Contract Reader](https://www.contractreader.io/)
- [EVM Storage](https://evm.storage/)

## Signature Databases
Signature databases are useful for identifying common patterns and signatures in smart contracts which could indicate potential issues:
- [4byte Directory](https://www.4byte.directory/)
- [Etherface](https://www.etherface.io/hash)
- [Openchain Signatures](https://openchain.xyz/signatures)
- [CyberChef](https://gchq.github.io/CyberChef/): Create hash from input.

## Misc
Miscellaneous tools that provide various functionalities which can be useful in different scenarios during smart contract auditing:
- [TokenSniffer](https://tokensniffer.com/): Detect fraud tokens.
- [EVM Codes](https://www.evm.codes/?fork=shanghai): Solidity opcodes with examples of usage.
- [Revoke Cash](https://revoke.cash/): Revoke your own token approvals.
- [Full Precision Calculator](https://www.mathsisfun.com/calculator-precision.html): Useful when auditing protocols with tokens and checking correct math operations with tokens.


## And this is not all...
> There's also a great resource - [Swiss Knife](https://swiss-knife.xyz/) with tools like epoch-converter, tx explorer, eth/hex converters, and transaction caller.

> [Remix](https://remix.ethereum.org/) - An Online Solidity IDE to develop and run Solidity code in a browser, debug transactions, even on bytecode level. It comes with many useful code plugins.

> [Etherscan](https://etherscan.io/) houses converter tools, dex/token tracker tools, and contract AI explorer tools. All these tools can be found under the 'More' item in the top menu.

> For those hungry for more tools, check out this list of [90+ tools](https://www.alchemy.com/top/solidity-tools).



## In conclusion...
...these tools are designed to accelerate your smart contract research and aid in uncovering more bugs, ensuring a robust and secure smart contract environment. Happy coding and auditing folks :-)



