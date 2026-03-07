# Vbucks-for-kill-and-win-Erbium
This is for people who want to skid and make a really good skiddy project or use it for reference, dm **@ghostcubert** on discord if you have any issues!

### Took inspo from https://github.com/kovryn/Vbucks-on-kill-and-win-GS-PART- Star his repository, Credit [Ghostcubert](https://github.com/ghostcubert) and [TheRevisitor](https://github.com/TheRevisitor) ifyou use it in a "project"

## Tutorial: [Here](https://youtu.be/hmqNHJz8qXc)

# Get started
### Open erbium.sln, click "show all files" on visual studio explorer, include libcurl folder, and other files with the red "-" sign in the project, including curl.h
Then open FortniteGame/Public/FortPlayerStateAthena.h, at the bottom add these, make sure they are inside the brackets { }:
```
DEFINE_FUNC(GetPlayerName, FString);
```
Save, now open FortniteGame/Private/FortPlayerControllerAthena.cpp
at the top below the header add these ones
```
#include "../../libcurl/curl.h"
#include <thread>
#include <string>
```

Now below the headers add this code replace 127.0.0.1:3551 to your reload backend ip and port, change "ur-api-key" to your api key in config, make sure the route is correct, in this code its the default one, /api/reload/vbucks:

```
void RewardVBucks(std::string Username, std::string Reason) {
    CURL* curl = curl_easy_init();
    if (curl) {
        char* output = curl_easy_escape(curl, Username.c_str(), Username.length());
        std::string SafeUsername = output;
        curl_free(output);

        std::string ApiKey = "ur-api-key"; //Your api key in config
        std::string BaseUrl = "http://127.0.0.1:3551/api/reload/vbucks"; //Backend Ip:port/route
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

## Kill
Now look for "KillerPlayerState->Kills++;" now make some space and make sure the code is below "KillerPlayerState->Kills++;" but above "KillerPlayerState->OnRep_Kills();"

```
auto* AthenaPS = (AFortPlayerStateAthena*)KillerPlayerState;
if (AthenaPS) {
    std::string KillerName = AthenaPS->GetPlayerName().ToString().c_str();
    std::thread([KillerName]() {
        RewardVBucks(KillerName, "Kill"); //Rewards vbucks u put for kill in config, each so for EX: 3 kills = 75 vbucks since 25 vbucks per kill
    }).detach();
}
```

## Win
Now look for "if (VersionInfo.FortniteVersion >= 16)" after the end of the brackets aka after the end statement add this code, make sure its below the "if (VersionInfo.FortniteVersion >= 16)" statement but above the "if (KillerPlayerState != PlayerState && VersionInfo.FortniteVersion >= 19)" statement

```
auto* WinnerPS = (AFortPlayerStateAthena*)KillerPlayerState;
if (WinnerPS) {
    std::string WinnerName = WinnerPS->GetPlayerName().ToString().c_str();
    std::thread([WinnerName]() {
        RewardVBucks(WinnerName, "Win"); //Rewards vbucks u put for win in config
    }).detach();
}
```

Now its done, enjoy skidding :)
