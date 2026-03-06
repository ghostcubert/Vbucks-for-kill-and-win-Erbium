# Vbucks-for-kill-and-win-Erbium
This is for people who want to skid and make a really good skiddy project or use it for reference

## Took inspo from https://github.com/kovryn/Vbucks-on-kill-and-win-GS-PART- Star his repository

# Get started
### Open erbium.sln, click "show all files" on visual studio explorer, include libcurl folder in the project, then open the folder and include libcurl.h
Then open FortniteGame/Public/AthenaCharacter.h, at the bottom add these, make sure they are inside the brackets {}:
```
DEFINE_FUNC(GetPlayerName, FString);
DEFINE_PROP(AccountId, FString);
```
Save, now opem FortniteGame/Private/FortPlayerControllerAthena.cpp
at the top below the header add these ones
```
#include "../../libcurl/curl.h"
#include <thread>
#include <string>
```

Now below the include headers add this code replace 127.0.0.1:3551 to your reload backend ip and port, change "ur-api-key" to your api key in config, make sure the route is correct, in this code its the default one, /api/reload/vbucks:

```
void RewardVBucks(std::string Username, std::string Reason) {
    CURL* curl = curl_easy_init();
    if (curl) {
        char* output = curl_easy_escape(curl, Username.c_str(), Username.length());
        std::string SafeUsername = output;
        curl_free(output);

        std::string ApiKey = "ur-api-key";
        std::string BaseUrl = "http://127.0.0.1:3551/api/reload/vbucks";
        std::string FullUrl = BaseUrl + "?apikey=" + ApiKey +
            "&username=" + SafeUsername +
            "&reason=" + Reason;

        curl_easy_setopt(curl, CURLOPT_URL, FullUrl.c_str());
        curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
        curl_easy_setopt(curl, CURLOPT_TIMEOUT, 5L);

        CURLcode res = curl_easy_perform(curl);
        if (res != CURLE_OK) {
            printf("Vbucks curl error: %s\n", curl_easy_strerror(res));
        }
        curl_easy_cleanup(curl);
    }
}
```

Now look for "KillerPlayerState->Kills++;" now make some space and make sure the code is below "KillerPlayerState->Kills++;" but above "KillerPlayerState->OnRep_Kills();"

```
auto* AthenaPS = (AFortPlayerStateAthena*)KillerPlayerState;
if (AthenaPS) {
    std::string KillerName = AthenaPS->GetPlayerName().ToString().c_str();
    std::thread([KillerName]() {
        RewardVBucks(KillerName, "Kill");
        }).detach();
}
```

Now look for "if (VersionInfo.FortniteVersion >= 16)" after the end of the brackets aka after the end statement add this code, make sure its below the "if (VersionInfo.FortniteVersion >= 16)" statement but above the "if (KillerPlayerState != PlayerState && VersionInfo.FortniteVersion >= 19)" statement

```
auto* WinnerPS = (AFortPlayerStateAthena*)KillerPlayerState;
if (WinnerPS) {
    std::string WinnerName = WinnerPS->GetPlayerName().ToString().c_str();

    printf("[V-BUCKS] Winner identified: %s. Sending Victory Reward!\n", WinnerName.c_str());

    std::thread([WinnerName]() {
        RewardVBucks(WinnerName, "Win");
        }).detach();
}
```

Now its done, enjoy skidding :)
