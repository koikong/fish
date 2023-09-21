<!DOCTYPE html>
<html>
<head>
    <title>Encrypt and Decrypt</title>
</head>
<body>
    <h1>Encrypt and Decrypt</h1>

    <h2>Commands:</h2>
    <p>1. <strong>encrypt/message/password:</strong> メッセージを暗号化します。</p>
    <p>2. <strong>decrypt/password:</strong> 暗号文を復号化します。</p>
    <p>3. <strong>help:</strong> ヘルプを表示します。</p>
    <p>4. <strong>exit:</strong> プログラムを終了します。</p>

    <form id="commandForm">
        <label for="commandInput">コマンドを入力してください:</label>
        <input type="text" id="commandInput" required>
        <button type="button" id="submitButton">実行</button>
    </form>

    <div id="outputArea"></div>

    <script>
        function splitMessage(message) {
            return [...message].map(c => c.charCodeAt(0));
        }

        function splitPassword(password) {
            return [...password].map(c => c.charCodeAt(0));
        }

        function encryptMessage(message, password) {
            const cleartext = splitMessage(message);
            const passwordCodes = splitPassword(password);
            const repeatedPassword = (passwordCodes.concat(...Array(Math.floor(cleartext.length / passwordCodes.length)).fill(passwordCodes))).concat(passwordCodes.slice(0, cleartext.length % passwordCodes.length));

            const encryptedValue = [];
            for (let i = 0; i < cleartext.length; i++) {
                const diff = cleartext[i] - repeatedPassword[i];
                const bigger = diff < 0 ? 1 : 0;
                encryptedValue.push(Math.abs(diff), bigger);
            }

            return encryptedValue;
        }

        function decryptMessage(encryptedValue, password) {
            const passwordCodes = splitPassword(password);
            const repeatedPassword = (passwordCodes.concat(...Array(Math.floor(encryptedValue.length / passwordCodes.length)).fill(passwordCodes))).concat(passwordCodes.slice(0, encryptedValue.length % passwordCodes.length));

            const decryptedMessageUnicode = [];
            for (let i = 0; i < encryptedValue.length; i += 2) {
                const diff = encryptedValue[i];
                const bigger = encryptedValue[i + 1];
                if (bigger === 1) {
                    decryptedMessageUnicode.push(repeatedPassword[i / 2] + diff);
                } else {
                    decryptedMessageUnicode.push(repeatedPassword[i / 2] - diff);
                }
            }

            const decryptedMessage = String.fromCharCode(...decryptedMessageUnicode);
            return decryptedMessage;
        }

        function printHelp() {
            return "コマンド一覧:\n- encrypt/message/password: メッセージを暗号化します。\n- decrypt/password: 暗号文を復号化します。\n- help: ヘルプを表示します。\n- exit: プログラムを終了します。\n\nコマンドの文型と例:\n1. encrypt/message/password:\n- 文型: encrypt/HelloWorld/MySecretPassword\n- 例: encrypt/HelloWorld/MySecretPassword\n2. decrypt/password:\n- 文型: decrypt/暗号化された値\n- 例: decrypt/[8, 1, 2, 0, 5, 1, 1, 1, 0, 2]\n3. help:\n- 文型: help\n- 例: help\n4. exit:\n- 文型: exit\n- 例: exit";
        }

        function main() {
            const commandForm = document.getElementById("commandForm");
            const commandInput = document.getElementById("commandInput");
            const submitButton = document.getElementById("submitButton");
            const outputArea = document.getElementById("outputArea");

            submitButton.addEventListener("click", function () {
                const command = commandInput.value.trim();

                if (command === "help") {
                    outputArea.textContent = printHelp();
                } else if (command === "exit") {
                    // プログラムを終了する処理を追加
                    outputArea.textContent = "プログラムを終了しました。";
                } else if (command.startsWith("encrypt/")) {
                    const args = command.split("/");
                    if (args.length === 3) {
                        const message = args[1];
                        const password = args[2];
                        const encryptedValue = encryptMessage(message, password);
                        outputArea.textContent = "暗号化された値: " + JSON.stringify(encryptedValue);
                    } else {
                        outputArea.textContent = "コマンドの引数が不正です。";
                    }
                } else if (command.startsWith("decrypt/")) {
                    const args = command.split("/");
                    if (args.length === 2) {
                        const encryptedValue = JSON.parse(args[1]);
                        const password = prompt("パスワードを入力してください: ");
                        if (password === null) {
                            outputArea.textContent = "パスワードが必要です。";
                        } else {
                            const decryptedMessage = decryptMessage(encryptedValue, password);
                            outputArea.textContent = "復号化されたメッセージ: " + decryptedMessage;
                        }
                    } else {
                        outputArea.textContent = "コマンドの引数が不正です。";
                    }
                } else {
                    outputArea.textContent = "無効なコマンドです。";
                }
            });
        }

        main();
    </script>
</body>
</html>
