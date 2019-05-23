# Gear 360 modding
Repository for Samsung Gear 360 (2017) modding

After preliminary testing, it seems that Gear 360 (2017) is very very similar to NX1/500 and it even shares a lot of firmware with them even the parts that make no sense on this camera (like aperture control, touchscreen LCD, etc).

Quick note - it is possible to gain telnet access to the camera by doing the following:
- Download the file (https://github.com/ottokiksmaler/gear360_modding/raw/master/gear360_mods_SD.zip) and extract it to the root directory of the microSD card (so that it contains ```DCIM``` and ```mods``` directories and ```info.tg```, ```mod.sh``` and ```nx_cs.adj``` files). Put the card in the camera and power on the camera.
- You should see the blue light above the Power button light up for a second.
- Double-click the [Power] button on the camera. You should see a top light cycle green-orange-green to indicate it's working now.
- Select connecting to iOS (long press Menu, then menu again to change to iOS and then click main shooting [OK] button).
- Connect to the Wi-Fi access point created by the camera (Gear 360), the password is shown on the camera screen.
- Open the browser and go to http://192.168.43.1:8888 (assuming your camera is also using 192.168.43.1 as the IP, it seems all are, but you can check yout IP from GUI or from the command line by typing ```ipconfig``` on Windows or ```ifconfig``` or ```ip addr ls``` on Linux.

The provided script that is triggered by double-click of the Power button does the following:
- Starts the telnet server on port 23 - for playing around and testing
- Starts the FTP server on port 21 - for file transfer
- Starts the HTTP server on port 8888 - for browsing the very simple gallery, taking a photo, taking 5 photos, changing mode, setting the current ISO and MAX AUTO ISO, as well as showing all USRLIST variables availavle and executing a remote shell command on the camera without telnet
- Does some inital settings **I** find useful (you are free to change anything and everything):
```
# SET PARAMS
# set ISO to 100
st cap capdtm setusr 5 0x050001
# set AUTO ISO MAX to 125
st cap capdtm setusr 64 0x400000
# set mode 0-movie, 1-smartauto, 2-program, 3-aperture, 4-shutter, 5-manual, 6-imode, 7-magic, 8-wifi, 9-scene, A-smartpro, B-SAS, 
st cap capdtm setusr 0 4
# set mode 0-movie, 1-smartauto, 2-program, 3-aperture, 4-shutter, 5-manual, 6-imode, 7-magic, 8-wifi, 9-scene, A-smartpro, B-SAS, 
st cap capdtm setusr 1 0x10004
# ISO NR - high ISO noise reduction - OFF
st cap capdtm setusr 30 0x1e0000
# LTNR - long time noise reduction - OFF
st cap capdtm setusr 31 0x1f0000
```

Almost everything I tried [works the same way as on NX1/NX500](https://github.com/ottokiksmaler/nx500_nx1_modding/blob/master/ST%20Commands.md) including [ST CAP commands](https://github.com/ottokiksmaler/nx500_nx1_modding/blob/master/ST_CAP_CAPDTM.md), for example, to take a photo ```st key click s2```, to "click" menu button ```st key click menu```, and so on.

One thing that annoyed me was lowest maximum ISO of 400 (way to high for such a small sensor), but ```st cap capdtm``` to the rescue:
```
[root@drime5 ~]# st cap capdtm usrlist         
     +-------------------------------+-------------------------------+----------+--------+
     + Data ID Name                  | Param ID Name                 + ID(Hex)  +ID(Dec) +
     +---------------------------------------------------------------+----------+--------+
[  0]| USERDATA_DIALMODE             | DIALMODE_PROGRAM              |0x00000002|       2|
[  1]| USERDATA_SHOOTINGMODE         | SHOOTINGMODE_PROGRAM          |0x00010001|   65537|
[  2]| USERDATA_IMAGESIZE            | IMAGESIZE_DUAL                |0x0002000F|  131087|
[  3]| USERDATA_IMAGEASPECTRATIO     | IMAGEASPECTRATIO_IMAGEAR_3_2  |0x00030000|  196608|
[  4]| USERDATA_IMAGEQUALITY         | IMAGEQUALITY_SUPERFINE        |0x00040000|  262144|
[  5]| USERDATA_ISO                  | ISO_AUTO                      |0x00050000|  327680|
[  6]| USERDATA_WB                   | WB_AUTO                       |0x00060000|  393216|
[  7]| USERDATA_AFMODE               | AFMODE_MANUAL                 |0x00070003|  458755|
[  8]| USERDATA_AFAREA               | AFAREA_SELECTION              |0x00080000|  524288|
[  9]| USERDATA_FACEDETECT           | FACEDETECT_OFF                |0x00090000|  589824|
[ 10]| USERDATA_DRIVE                | DRIVE_SINGLE                  |0x000A0001|  655361|
[ 11]| USERDATA_BURSTRATE            | BURSTRATE_RATE10FPS           |0x000B0002|  720898|
[ 12]| USERDATA_FLASHMODE            | FLASHMODE_OFF                 |0x000C0000|  786432|
[ 13]| USERDATA_METERING             | METERING_MULTI                |0x000D0002|  851970|
[ 14]| USERDATA_MOVIEMETERING        | MOVIEMETERING_MULTI           |0x000E0002|  917506|
[ 15]| USERDATA_LINKAE2AF            | LINKAE2AF_ON                  |0x000F0001|  983041|
[ 16]| USERDATA_BRIGHTNESSADJUSTGUIDE| BRIGHTNESSADJUSTGUIDE_OFF     |0x00100000| 1048576|
[ 17]| USERDATA_FRAMEEXPOSURE        | FRAMEEXPOSURE_OFF             |0x00110000| 1114112|
[ 18]| USERDATA_MULTIEXPOSURE        | MULTIEXPOSURE_OFF             |0x00120000| 1179648|
[ 19]| USERDATA_MULTIEXPOSURETYPE    | MULTIEXPOSURETYPE_SAVEALL     |0x00130000| 1245184|
[ 20]| USERDATA_PW                   | PW_STANDARD                   |0x00140000| 1310720|
[ 21]| USERDATA_SMARTRANGE           | SMARTRANGE_ON                 |0x00150001| 1376257|
[ 22]| USERDATA_MOVIESMARTRANGE      | MOVIESMARTRANGE_OFF           |0x00160000| 1441792|
[ 23]| USERDATA_AFPRIORITYRLS        | AFPRIORITYRLS_ON              |0x00170003| 1507331|
[ 24]| USERDATA_OIS                  | OIS_OFF                       |0x00180000| 1572864|
[ 25]| USERDATA_FACETONE             | FACETONE_LEVEL1               |0x00190000| 1638400|
[ 26]| USERDATA_FACERETOUCH          | FACERETOUCH_LEVEL1            |0x001A0000| 1703936|
[ 27]| USERDATA_SMARTART             | SMARTART_OFF                  |0x001B0000| 1769472|
[ 28]| USERDATA_SMARTARTLEVEL        | SMARTARTLEVEL_LEVEL0          |0x001C0000| 1835008|
[ 29]| USERDATA_EVSTEP               | EVSTEP_ONETHIRD               |0x001D0000| 1900544|
[ 30]| USERDATA_ISONR                | ISONR_MID                     |0x001E0002| 1966082|
[ 31]| USERDATA_LTNR                 | LTNR_ON                       |0x001F0001| 2031617|
[ 32]| USERDATA_AEBRKORDER           | AEBRKORDER_ZMP                |0x00200000| 2097152|
[ 33]| USERDATA_AEBRKAREA            | AEBRKAREA_1OVER3              |0x00210000| 2162688|
[ 34]| USERDATA_WBBRKSET             | WBBRKSET_AB3                  |0x00220000| 2228224|
[ 35]| USERDATA_PWBRKSTANDARD        | PWBRKSTANDARD_SET             |0x00230001| 2293761|
[ 36]| USERDATA_PWBRKVIVID           | PWBRKVIVID_SET                |0x00240001| 2359297|
[ 37]| USERDATA_PWBRKPORTRAIT        | PWBRKPORTRAIT_SET             |0x00250001| 2424833|
[ 38]| USERDATA_PWBRKLANDSCAPE       | PWBRKLANDSCAPE_SET            |0x00260001| 2490369|
[ 39]| USERDATA_PWBRKFOREST          | PWBRKFOREST_SET               |0x00270001| 2555905|
[ 40]| USERDATA_PWBRKRETRO           | PWBRKRETRO_SET                |0x00280001| 2621441|
[ 41]| USERDATA_PWBRKCOOL            | PWBRKCOOL_SET                 |0x00290001| 2686977|
[ 42]| USERDATA_PWBRKCALM            | PWBRKCALM_SET                 |0x002A0001| 2752513|
[ 43]| USERDATA_PWBRKCLASSIC         | PWBRKCLASSIC_SET              |0x002B0001| 2818049|
[ 44]| USERDATA_PWBRKCUSTOM1         | PWBRKCUSTOM1_SET              |0x002C0001| 2883585|
[ 45]| USERDATA_PWBRKCUSTOM2         | PWBRKCUSTOM2_SET              |0x002D0001| 2949121|
[ 46]| USERDATA_PWBRKCUSTOM3         | PWBRKCUSTOM3_SET              |0x002E0001| 3014657|
[ 47]| USERDATA_PWBRKCUSTOM4         | PWBRKCUSTOM4_SET              |0x002F0001| 3080193|
[ 48]| USERDATA_AFLIGHT              | AFLIGHT_LOW                   |0x00300003| 3145731|
[ 49]| USERDATA_AEL                  | AEL_AEL                       |0x00310000| 3211264|
[ 50]| USERDATA_COLORSPACE           | COLORSPACE_SRGB               |0x00320000| 3276800|
[ 51]| USERDATA_QUICKVIEWTIME        | QUICKVIEWTIME_1SEC            |0x00330001| 3342337|
[ 52]| USERDATA_MONITOROUT           | MONITOROUT_LCD                |0x00340000| 3407872|
[ 53]| USERDATA_HDMIOUT              | HDMIOUT_1080I                 |0x00350003| 3473411|
[ 54]| USERDATA_MOVIESIZE            | MOVIESIZE_DUAL_4096           |0x00360000| 3538944|
[ 55]| USERDATA_MOVIEFRAMERATE       | MOVIEFRAMERATE_FPS24          |0x00370002| 3604482|
[ 56]| USERDATA_MOVIEFADER           | MOVIEFADER_OFF                |0x00380000| 3670016|
[ 57]| USERDATA_MOVIEAE              | MOVIEAE_P                     |0x00390000| 3735552|
[ 58]| USERDATA_3DMOVIEFRAMERATE     | F3DMOVIEFRAMERATE_FPS30       |0x003A0001| 3801089|
[ 59]| USERDATA_LDC                  | LDC_ON                        |0x003B0001| 3866625|
[ 60]| USERDATA_ISOSTEP              | ISOSTEP_ONE                   |0x003C0001| 3932161|
[ 61]| USERDATA_ISOEXPANSION         | ISOEXPANSION_ON               |0x003D0001| 3997697|
[ 62]| USERDATA_SMARTFILTERTYPE      | SMARTFILTERTYPE_OFF           |0x003E0000| 4063232|
[ 63]| USERDATA_SMARTFILTERSIZE      | SMARTFILTERSIZE_SIZE0         |0x003F0000| 4128768|
[ 64]| USERDATA_ISOAUTOMAX           | ISOAUTOMAX_ISO400             |0x00400005| 4194309|
[ 65]| USERDATA_MFASSIST             | MFASSIST_ENLARGEX5            |0x00410001| 4259841|
[ 66]| USERDATA_FOCUSPEAKING         | FOCUSPEAKING_OFF              |0x00420000| 4325376|
[ 67]| USERDATA_HDR                  | HDR_OFF                       |0x00430000| 4390912|
[ 68]| USERDATA_FDMODE               | FDMODE_OFF                    |0x00440000| 4456448|
[ 69]| USERDATA_IZOOM                | IZOOM_X1                      |0x00450000| 4521984|
[ 70]| USERDATA_3DSHOT               | F3DSHOT_OFF                   |0x00460000| 4587520|
[ 71]| USERDATA_3DAUTOMODE           | F3DAUTOMODE_OFF               |0x00470000| 4653056|
[ 72]| USERDATA_DMF                  | DMF_OFF                       |0x00480000| 4718592|
[ 73]| USERDATA_FASTCONTINUOUS       | FASTCONTINUOUS_OFF            |0x00490000| 4784128|
[ 74]| USERDATA_SMARTMOVIE           | SMARTMOVIE_OFF                |0x004A0000| 4849664|
[ 75]| USERDATA_LENSEFFECT           | LENSEFFECT_OFF                |0x004B0000| 4915200|
[ 76]| USERDATA_HDRLEVEL             | HDRLEVEL_LV2                  |0x004C0001| 4980737|
[ 77]| USERDATA_HDRARTLEVEL          | HDRARTLEVEL_LV1               |0x004D0000| 5046272|
[ 78]| USERDATA_LLSLEVEL             | LLSLEVEL_LV1                  |0x004E0000| 5111808|
[ 79]| USERDATA_OBJECTTRACKING       | OBJECTTRACKING_OFF            |0x004F0000| 5177344|
[ 80]| USERDATA_PANORAMATYPE         | PANORAMATYPE_2D               |0x00500000| 5242880|
[ 81]| USERDATA_SMARTPROMODE         | SMARTPROMODE_BEAUTYFACE       |0x00510000| 5308416|
[ 82]| USERDATA_SMARTPROLEVEL        | SMARTPROLEVEL_M2              |0x00520000| 5373952|
[ 83]| USERDATA_EFS                  | EFS_OFF                       |0x00530000| 5439488|
[ 84]| USERDATA_WIFIMODE             | WIFIMODE_RVF                  |0x00540001| 5505025|
[ 85]| USERDATA_VIDEOOUT             | VIDEOOUT_NTSC                 |0x00550000| 5570560|
[ 86]| USERDATA_SMARTAUTORUNSTATE    | SMARTAUTORUNSTATE_OFF         |0x00560000| 5636096|
[ 87]| USERDATA_3DFORMAT             | F3DFORMAT_SEQUENTIAL          |0x00570000| 5701632|
[ 88]| USERDATA_FOCUSPEAKINGLEVEL    | FOCUSPEAKINGLEVEL_OFF         |0x00580000| 5767168|
[ 89]| USERDATA_LENSMOUNT            | LENSMOUNT_NX_MOUNT            |0x00590000| 5832704|
[ 90]| USERDATA_OVEREXPGUIDE         | OVEREXPGUIDE_OFF              |0x005A0000| 5898240|
[ 91]| USERDATA_SHUTTERMIN           | SHUTTERMIN_AUTO_0             |0x005B0001| 5963777|
[ 92]| USERDATA_DEPTHBRKAREA         | DEPTHBRKAREA_1OVER3           |0x005C0000| 6029312|
[ 93]| USERDATA_INTERVAL             | INTERVAL_OFF                  |0x005D0000| 6094848|
[ 94]| USERDATA_TIMELAPSE            | TIMELAPSE_OFF                 |0x005E0000| 6160384|
[ 95]| USERDATA_VIDEOSTABILIZER      | VIDEOSTABILIZER_OFF           |0x005F0000| 6225920|
[ 96]| USERDATA_PANORAMATHUMBNAIL    | PANORAMATHUMBNAIL_OFF         |0x00600000| 6291456|
[ 97]| USERDATA_EXTERNALFLASHMODE    | EXTERNALFLASHMODE_ATTL        |0x00610000| 6356992|
[ 98]| USERDATA_WFLASHMODE           | WFLASHMODE_OFF                |0x00620000| 6422528|
[ 99]| USERDATA_WFLASHCH             | WFLASHCH_1                    |0x00630000| 6488064|
[100]| USERDATA_WFLASHMASTERMODE     | WFLASHMASTERMODE_OFF          |0x00640000| 6553600|
[101]| USERDATA_WFLASHGROUPAMODE     | WFLASHGROUPAMODE_OFF          |0x00650000| 6619136|
[102]| USERDATA_WFLASHGROUPBMODE     | WFLASHGROUPBMODE_OFF          |0x00660000| 6684672|
[103]| USERDATA_WFLASHGROUPCMODE     | WFLASHGROUPCMODE_OFF          |0x00670000| 6750208|
[104]| USERDATA_HDMI3DFORMAT         | HDMI3DFORMAT_SEQUENTIAL       |0x00680000| 6815744|
[105]| USERDATA_LENSZOOMSPEED        | LENSZOOMSPEED_LOW             |0x00690000| 6881280|
[106]| USERDATA_LENSMFSENSITIVITY    | LENSMFSENSITIVITY_NORMAL      |0x006A0000| 6946816|
[107]| USERDATA_SEPARATEAEAREA       | SEPARATEAEAREA_OFF            |0x006B0000| 7012352|
[108]| USERDATA_OLEDCOLOR            | OLEDCOLOR_OFF                 |0x006C0000| 7077888|
[109]| USERDATA_BABYMONITOR          | BABYMONITOR_OFF               |0x006D0000| 7143424|
[110]| USERDATA_MULTIMOTION          | MULTIMOTION_SLOW_0_25X        |0x006E0000| 7208960|
[111]| USERDATA_SENSORTESTMODE       | SENSORTESTMODE_OFF            |0x006F0000| 7274496|
[112]| USERDATA_AUTOSHUTTERMODE      | AUTOSHUTTERMODE_BASEBALL      |0x00700000| 7340032|
[113]| USERDATA_RVFDPSIZE            | RVFDPSIZE_FHD                 |0x00710000| 7405568|
[114]| USERDATA_RVFDPFRAMRATE        | RVFDPFRAMRATE_FPS30           |0x00720004| 7471108|
[115]| USERDATA_SYSTEMFREQENCYSTATE  | SYSTEMFREQENCYSTATE_LIVEVIEW  |0x00730000| 7536640|
[116]| USERDATA_HDMI3D               | HDMI3D_OFF                    |0x00740000| 7602176|
[117]| USERDATA_ADJUSTSHUTTERTYPE    | ADJUSTSHUTTERTYPE_MECHA       |0x00750000| 7667712|
[118]| USERDATA_MOVIE_GAMMA_CONTROL  | MOVIE_GAMMA_CONTROL_STANDARD  |0x00760000| 7733248|
[119]| USERDATA_MOVIE_LUMINANCE_LEVEL| MOVIE_LUMINANCE_LEVEL_0_255   |0x00770000| 7798784|
[120]| USERDATA_MOVIE_AF_MODE        | MOVIE_AF_MODE_MANUAL          |0x00780002| 7864322|
[121]| USERDATA_GOLFREVERSE          | GOLFREVERSE_ORIGINAL          |0x00790000| 7929856|
[122]| USERDATA_RAWQUALITY           | RAWQUALITY_LOSSLESS           |0x007A0000| 7995392|
[123]| USERDATA_RAW_SAVE             | RAW_SAVE_ON                   |0x007B0001| 8060929|
[124]| USERDATA_SSIF_RAW             | SSIF_RAW_OFF                  |0x007C0000| 8126464|
[125]| USERDATA_RAW_BIT              | RAW_BIT_14BIT                 |0x007D0000| 8192000|
[126]| USERDATA_RAW_PACK             | RAW_PACK_PACK                 |0x007E0000| 8257536|
[127]| USERDATA_FLIPSTATE            | FLIPSTATE_UP                  |0x007F0001| 8323073|
[128]| USERDATA_SAVEAS_FLIPPED       | SAVEAS_FLIPPED_OFF            |0x00800000| 8388608|
[129]| USERDATA_LENS_MODE            | LENS_MODE_360                 |0x00810000| 8454144|
[130]| USERDATA_TIMELAPSESIZE        | TIMELAPSESIZE_DUAL_2560       |0x00820001| 8519681|
[131]| USERDATA_OSCMODE              | OSCMODE_OFF                   |0x00830000| 8585216|
[132]| USERDATA_MAINLENS             | MAINLENS_REAR                 |0x00840001| 8650753|
     +-------------------------------+-------------------------------+----------+--------+
[root@drime5 ~]# st cap capdtm getusr 64
UserData is ISOAUTOMAX_ISO400 (0x400005)
[root@drime5 ~]# st cap capdtm setusr 64 0x400001
UserData is set
[root@drime5 ~]# st cap capdtm getusr 64         
UserData is ISOAUTOMAX_ISO160 (0x400001)
[root@drime5 ~]# st cap capdtm setusr 64 0x400000
UserData is set
[root@drime5 ~]# st cap capdtm getusr 64         
UserData is ISOAUTOMAX_ISO125 (0x400000)
```
Now it's ISO 125 - verified in practice.

That's it for now, wonder what people would like to do to the camera?
