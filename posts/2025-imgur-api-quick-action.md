# 擺脫 HackMD 圖片上傳 1MB 的限制：整合 Imgur API 到 MacOS 快速動作

在使用 HackMD 時，如果要上傳一張圖片，當圖片超過 1MB 時，總會被告知無法上傳，然而一張螢幕截圖往往就超過 1MB，除了壓縮圖片犧牲畫質外，就沒有其他簡單又快速的方式嗎？

筆者使用 Imgur API 搭配 MacOS 的 Automator 應用程式，打造一個快速動作，只要對圖片「點擊右鍵」>「快速動作」，就能把圖片自動上傳到 Imgur 並複製該圖片之 URL 到剪貼簿。Imgur API 提供每天可上傳 1250 張圖片，每張圖片最大可達 20MB，對於一般的使用而言，基本上是用不完的。

接下來，讓筆者帶著你/妳一起進行前置設定，並享受快速動作帶來的方便性吧！

---

1. 前往 [https://api.imgur.com/oauth2/addclient](https://api.imgur.com/oauth2/addclient)
2. 登入或註冊 Imgur 帳號
3. 填寫表格：
    * 自訂應用程式名稱
    * 選擇「OAuth 2 application without a callback URL」
    * 提供電子郵件
    ![](https://i.imgur.com/KwNFNRM.png)
4. 提交後會獲得 Client ID，就是 API 金鑰
5. 在 MacOS 上使用 Spotlight（cmd + 空白）搜尋「Automator」，開啟 Automator 應用程式。
6. 新增一個「快速動作」檔案。
    ![](https://i.imgur.com/YPJQrVa.jpeg)
7. 右上方各欄位分別選擇「圖片」、「任何應用程式」，並選擇自己想要的圖示。
    ![](https://i.imgur.com/VvPTmOd.jpeg)
8. 在左側搜尋欄輸入「shell」並拖曳出「執行 Shell 指令」。
    ![](https://i.imgur.com/M0iF8Ai.png)
9.  上方兩個下拉選單分別選擇「/bin/bash」、「作為引數」
    ![](https://i.imgur.com/Sjo8BW2.png)
10. 在指令區貼上下方指令，並替換上你的 API 金鑰：
    ```bash=
    #!/bin/bash

    # 替換為你的 Imgur API 金鑰
    API_KEY="替換為 API 金鑰"

    # 處理傳入的圖像檔案
    for f in "$@"
    do
        # 顯示通知
        osascript -e "display notification \"正在上傳到 Imgur\" with title \"上傳到 Imgur\""

        echo "正在處理檔案: $f"

        # 使用 curl 上傳圖像到 Imgur
        echo "開始上傳到 Imgur..."
        response=$(curl -s -H "Authorization: Client-ID $API_KEY" -F "image=@$f" https://api.imgur.com/3/image)

        # 檢查是否成功上傳
        if [[ $response == *"success\":true"* ]]; then
            # 從回應中提取直接連結
            direct_link=$(echo "$response" | /usr/bin/python3 -c 'import json,sys;print(json.load(sys.stdin)["data"]["link"])')

            echo "上傳成功！連結: $direct_link"

            # 多種方法嘗試將連結複製到剪貼簿
            # 方法1: 使用 pbcopy
            echo -n "$direct_link" | pbcopy

            # 方法2: 使用 osascript
            osascript -e "set the clipboard to \"$direct_link\""

            # 方法3: 使用 Automator 變數傳遞
            echo "$direct_link" > /tmp/imgur_link.txt

            # 顯示通知
            osascript -e "display notification \"$direct_link 已複製到剪貼簿\" with title \"Imgur 上傳成功\""
        else
            echo "上傳失敗。回應: $response"

            # 顯示通知
            osascript -e "display notification \"上傳失敗。回應: $response\" with title \"Imgur 上傳失敗\""
        fi
    done

    # 輸出更詳細的資訊，方便排除問題
    echo "************** 執行結果 **************"
    echo "圖像已上傳到 Imgur"

    if [ -f "/tmp/imgur_link.txt" ]; then
        final_link=$(cat /tmp/imgur_link.txt)
        echo "連結: $final_link"

        # 再次嘗試複製到剪貼簿
        echo -n "$final_link" | pbcopy

        # 在結束時輸出連結，這樣可以被 Automator 的「複製到剪貼簿」動作獲取
        echo "$final_link"
    else
        echo "無法獲取上傳連結"
    fi

    echo "***************************************"
    ```
11. 存檔並命名為「Upload to Imgur」。
12. 接著就可以對著圖片檔「點擊右鍵」>「快速動作」>「Upload to Imgur」複製圖片連結。
    ![](https://i.imgur.com/Axqa1Ka.png)

---

如果有任何疑問或建議，歡迎來信詢問：sunyipingtw@icloud.com。