
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

# Installation

This section expects the requirements stated in the previous section to be met and this is how this section has been written.

  - **GeckoDriver**: The driver that **Selenium** uses to interact with **Firefox**.
 	 - Download the file from the **GitHub** repository: [Link][4]
 	 - Place it in */usr/bin* or */usr/local/bin* directory (you may need administrator permission).
  - **Beauty predictor**:  The predictor needs two files to be able to work.
 	 - **Face recognition model**: This model is the one used to recognize faces in photos. ([Link][5])
 	 - **Face beauty model**: This model is the one used to measure the beauty of a face. ([Link][6])
 	 -  Place both files in the *beauty* folder of the project and change their names to *model_human_face_detector.dat* and *model-ldl-resnet.h5* respectively. 
  - **Program dependencies**: The bot has some dependencies that must be installed in order to work. Those dependencies can be installed with the *requirements.txt* file:
 	 -  `pip install -r requirements.txt`
 
 **Note**: If you have both **Python 2** and **Python 3** installed on your system, use **pip3** instead of **pip**.
 
[4]: https://github.com/mozilla/geckodriver/releases
[5]: http://dlib.net/files/mmod_human_face_detector.dat.bz2
[6]: http://plong.perso.centrale-marseille.fr/visible/model-resnet.h5

# Configuration

There are some parameters that need to be set by the user, so the bot can work. Those parameters are in the */resources/constants.py* file.

 - **EXPAND_BUTTON_TEXT**: **Tinder** uses the same button to show more login options as to fix login issues. The text `MORE OPTIONS` must be *indicated in the same language in which the browser to be used is configured*. Check on the login screen how it is written, **it is case-sensitive**.
 - **SENT_TEXT_CHAT**: The text that **Tinder** displays in the chat when a message has been sent. The text `Sent` must be *indicated in the same language in which the **Tinder** account to be used is configured*. Check how it is written on the chat screen, **it is case-sensitive**.
 - **READ_TEXT_CHAT** (*For paid account profiles only*): The text that **Tinder** displays in the chat when a message has been read by the recipient. The text `Read` must be *indicated in the same language in which the **Tinder** account to be used is configured*. Check how it is written on the chat screen, **it is case-sensitive**.
 - **WAIT_MATCH_ANSWER**: Boolean value to specify whether the bot waits until the profile has responded or not before sending a message. It is recommend to set the value to **True** instead of **False** to *avoid flooding matching profiles with messages*.
 - **SILENT_MODE**: Boolean value to specify whether the bot starts the web browser with an interface or not. It is recommended to set the value to **True** instead of **False** to *avoid a greater consumption of resources*.
 - **DEFAULT_CHAT_MESSAGES**: The list of default messages that the bot will select when sending messages to the matching profiles.
 - **SCORE_THRESHOLD**: The *beauty threshold* that the bot will take into account to decide whether to *like* or *dislike* a profile.
 - **USERNAME**: The email associated with your **Facebook** account.
 - **PASSWORD**: The password associated with your **Facebook** account.

There are other constants in the file, but they should not be changed unless you know what you are doing or if you want to continue with the development of the bot.

It is highly recommended to use a **virtual environment** (*venv*), so the bot dependencies installation will not conflict with the packages installed on the system. By default, the *crontab* functionality expects to run in a virtual environment (*venv*), so the path of the virtual environment must be specified in the `crontab_script.sh`  file:

```
#!/bin/bash

source path_to_your_virtual_environment/bin/activate
python3 tinder_bot.py
```
If, on the other hand, you want to run the bot without a virtual environment (*venv*), the `source` line must be commented.

```
#!/bin/bash

# source path_to_your_virtual_environment/bin/activate
python3 tinder_bot.py
```

# How to use

The idea of this project is to implement the bot on a **Raspberry Pi** and leave it running without seeing what is it doing (although, it is recommended to see the log files occasionally to check that it is working as expected).

Since facial recognition and beauty prediction are time-consuming, it takes the bot about *120 minutes* (*2 hours*) to reach the daily limit of *100 likes* and the time also depends on the number of profiles discarded when they are invalid. The fewer profiles the bot has discarded, the less time the process will take.

To start the bot, you must grant it execution permissions or, otherwise, it cannot be executed. In addition, it also needs the permissions to be able to generate an entry on the *crontab* file (**Linux** systems only):

`chmod ugo+x tinder_bot.py`

If you are going to use the crontab functionality, you must also grant permissions to the bash script:

`chmod ugo+x crontab_script.sh`

After that, you can start the bot with:

`python3 tinder_bot.py`

You can decide what the bot can do by commenting (adding an *#* at the beginning of the line) or uncommenting (removing the *#* at the beginning of the line) some function calls:

```
bot = TinderBot() [1]
if bot.check_user_constants(): [2]
    bot.login() [3]
    bot.close_permission_popups() [4]
    bot.auto_swipe() [5]
    bot.collect_photos_matched_profiles() [6]
    bot.review_messages_from_matching_profiles() [7]
    bot.check_blurry_matches() [8] # DEPRECATED: It no longer works.
    bot.remove_photo_folder(bot.temporary_folder) [9]
    bot.check_crontab_entry() [10]
    bot.exit() [11]
```

 1. Initialize the bot. (**Mandatory**)
 2. Verifies that the values ​​entered by the user are of the correct type. (**Optional, but recommended**)
 3. Sign in to *Tinder* through *Facebook*. (**Mandatory**)
 4. Closes the *Tinder* pop-ups to request permission to detect the location, be able to send notifications and the cookie banner. (**Mandatory**)
 5. The bot tries to find your soulmate. (**Optional, although I'm not sure...**)
 6. The bot downloads the photos of the profiles with which you have had a match. (**Optional**)
     - This feature has a parameter, `skip_animation`, which is used to control when it is executed. If the value is **True** it means that it **runs after step 5** and if the value is **False** it means that it **runs without step 5**. *This is important because otherwise it will fail*.
 7. The bot scrolls through the list of your matching profiles and sends a predefined message chosen at random. (**Optional**)
     - To use this feature, you must have previously entered some values in the message list.
        - **By default, the bot will not send a message if the matching profile has not yet answered**. This behaviour it is controlled with the constant `WAIT_MATCH_ANSWER` in the file `/resources/constants.py`.
        - **Remember to always be respectful to everyone**.
        - Modify the line `DEFAULT_CHAT_MESSAGES = []` in the file `/resources/constants.py` with some values, for example `DEFAULT_CHAT_MESSAGES = ["Hi!", "How are you?", "How's your day going?"]`.
 8. **[DEPRECATED]** The bot gets the photo of the blurred profiles if the user has a **free account**. (**Optional**)
     - This option has **not been tested** on a **premium profile**.
     - This functionality **has stopped working** since *May 1, 2020*. Now it seems that **Tinder** checks if your account has permissions to access the original photo of the blurred thumbnail, so when the bot tries to obtain the original photo you get an access denied error instead of the original photo.
 9. Deletes the temporary folder where all the profile photos have been downloaded. (**Optional**)
 10. Sets the bot to start automatically after turning it off. (**Optional**)
       - This function **only works** on **Linux systems**.
       - If the bot has turned itself off because *there are no more profiles to display*, it sets itself to start again in *an hour*.
       - If the bot has turned itself off because *the daily limit on the free account has been reached*, it sets itself to start again in *12 hours and 5 minutes*. Although the **Tinder** cooldown lasts *12 hours*, the bot gives *5 minutes* of margin.
 11. Turns off the bot. (**Mandatory**)
       - The bot **will also turn itself off without this call** but **the driver instance will not be deleted**. The browser window **will remain open, consuming memory resources** if the *SILENT_MODE* it is not activated.

**Note 1**: All the photos downloaded, except for *gold_matches*, will be saved in their respective folder inside a folder called `profile_name - profile_age - number_of_photos`.

**Note 2**: The bot will automatically delete the *gold_matches* and *matched_photos* folders when you have no more matches.

# Limitations and considerations

- The bot has certain technical limitations that can cause some profiles that are valid from the user's point of view to be discarded.
  - If the photo has a partial face (it is cut off) the bot will fail in the beauty prediction phase as it cannot obtain the necessary points to perform the calculation, so that photo will be invalidated with a score of 0.
  - Although a face can be detected in the photo in the pre-beauty prediction phase, the photo can be discarded if the person appears too far away in the photo, and the face is not large enough to get all the points. In this case, the photo will also be invalidated with a score of 0.
  - Sometimes some profiles have pictures of drawings with human faces. If the drawing is very simple, the bot detects that it is not human, but if it is a realistic drawing, the bot can be confused and identify the photo as valid.

- There are some considerations to keep in mind when calculating the beauty score:
  - When calculating the beauty score, keep in mind that the same value will not always be obtained for the same person. The value obtained depends on the quality of the photo, the amount of light in the photo, and the expression of the face that appears in the photo. The score will also be affected if there are filters from **Instagram** or *another application* in the photo as they confuse the neural network.
  - Another aspect to consider when calculating the beauty of a profile is the extreme values. When analyzing profile photos, three options can happen in each photo if the person appears with more people because the beauty predictor returns the scores of all the faces found in each photo:
    - If the other people in the photo get a score similar to the person of the profile, those values ​​do not affect since it would be as if the owner of the profile has more photos uploaded.
    - If someone in the photo gets a much higher score than the person of the profile, that score will positively benefit him/her.
    - If someone in the photo gets a much lower score than the person of the profile, that score will negatively penalize him/her.
    - After consulting with several statistical friends, the easiest way to avoid these deviations is to use the median, since it ignores the extreme values of the intervals.

# Future work

At the time of writing this document and uploading the code, the bot works completely. This does not imply that this will be the case in the future as the web version of **Tinder** experiences changes and consequently the buttons and sections paths change.

Chatting functionality could also be improved. There is a specific case in which the bot can consider that the person has not responded if the last message the person sent is *Read* or *Sent* (in the language in which the **Tinder** account that uses the bot is configured).

Therefore, if at any time the bot stops working, the *xpaths* should be reviewed and updated.

# Demo

At first, I had thought of putting out a video with a demonstration of how the bot works but because I was not sure if I could show the profiles of people without their consent despite the fact they are exposed in a dating application (although on **YouTube** you can find videos where people show profiles) finally I decided not to release the video, so if you want to see the bot working you will have to install it and run it locally on your computer.

# References

 - **Beauty predict** project by **ustcqidi**: [GitHub][7]
 - **Tinder bot example** project by **aj-4**: [GitHub][8]

[7]: https://github.com/ustcqidi/BeautyPredict
[8]: https://github.com/aj-4/tinder-swipe-bot

# Licensing agreement
