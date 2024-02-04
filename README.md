
# Requisites

In order to use the bot it is necessary to have a compatible environment:

 - **Operative system**: *Linux* or *Windows* (The *crontab* functionality has been implemented with *Linux* systems in mind, not *Windows*).
 - **Python version**: The *minimum and necessary version* is **3.5** (due to dependence with *Path*) although the *desirable version* would be **3.7** (due the insertion-order preservation of dict objects).
 - **Browser**: **Firefox**  version  **>=** **60**. This bot has been designed to use the **GeckoDriver** and in the latest version they increased the minimum version of the browser to the version indicated at the beginning.
	 - Be sure the **tracking protection** is not enabled on the **Tinder** website, or the login button with 
 - **Tinder**: It is necessary to have an **active and valid** account to use the bot. 
	 - **You must unlock all the buttons in the selection panel**. Slide some profiles until the *rewind* button, the *super like* button, and the *boost* button are unlocked for the account. Otherwise, the bot cannot find the correct position of the *like* and *not like* buttons. 

# Project structure

In this section you can have a quick view of the project structure.

```
├── beauty
│   ├── model_human_face_detector.dat
│   └── model-ldl-resnet.h5
├── gold_matches (*)
├── logger.py
├── logs (*)
│   ├── critical.txt
│   ├── debug.txt
│   ├── error.txt
│   ├── information.txt
│   └── warning.txt
├── matched_photos (*)
├── predict.py
├── README.md
├── requirements.txt
├── resources
│   ├── constants.py
│   ├── firefox_xpaths.py
│   └── log_configuration.yaml
├── slipped_profiles (*)
└── tinder_bot.py
```

Directories marked with a (*) will be created by the bot as needed.
