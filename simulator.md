# Simulator and Run KStars

If you built kstars the first task is to find where the files are placed (I am assuming at the moment you did not do a *make install*).

So if kstars was cloned into *~/Dev/C++/Astro/kstars* (As mine was), and you created a build folder called *build* lets go there.

    cd ~/Dev/C++/Astro/kstars/build 

So from here we go down a little more

    cd bin/KStars.app/Contents/MacOS 

And we see with *ls*  the following

```text
dbus-daemon   indi_dreamfocuser_focus  indi_lx200_OnStep  indi_onfocus_focus  indi_script_telescope  indi_teenastro_focus
dbus-send   indi_dsc_telescope  indi_lx200_OpenAstroTech indi_openweathermap_weather indi_sestosenso_focus  indi_temma_telescope
gsc    indi_efa_focus   indi_lx200_pegasus_nyx101 indi_optec_wheel  indi_sestosenso2_focus  indi_terrans_powerboxgo_v2
indi_aaf2_focus   indi_eq500x_telescope  indi_lx200_TeenAstro  indi_paramount_telescope indi_setprop   indi_terrans_powerboxpro_v2
indi_aagsolo_weather  indi_esatto_focus  indi_lx200am5   indi_pegasus_flatmaster  indi_siefs_focus  indi_trutech_wheel
indi_activefocuser_focus indi_esattoarco_focus  indi_lx200ap_legacy  indi_pegasus_focuscube  indi_simulator_ccd  indi_universalror_dome
indi_alluna_tcs2  indi_eval   indi_lx200ap_v2   indi_pegasus_focuscube3  indi_simulator_dome  indi_ups_safety
indi_alpaca_dome  indi_Excalibur   indi_lx200autostar  indi_pegasus_ppb  indi_simulator_dustcover indi_uranus_weather
indi_alto   indi_falcon_rotator  indi_lx200basic   indi_pegasus_ppba  indi_simulator_focus  indi_usbdewpoint
indi_arduinost4   indi_falconv2_rotator  indi_lx200classic  indi_pegasus_prodigyMF  indi_simulator_gps  indi_usbfocusv3_focus
indi_astrolink4   indi_fcusb_focus  indi_lx200fs2   indi_pegasus_scopsoag  indi_simulator_guide  indi_vantage_weather
indi_astrolink4mini2  indi_flipflat   indi_lx200gemini  indi_pegasus_spb  indi_simulator_io  indi_wanderer_cover
indi_astromech_lpm  indi_gemini_flatpanel  indi_lx200generic  indi_pegasus_uch  indi_simulator_lightpanel indi_wanderer_dew_terminator
indi_astromechfoc  indi_gemini_focus  indi_lx200gotonova  indi_pegasus_upb  indi_simulator_receiver  indi_wanderer_eclipse
indi_astrometry   indi_getprop   indi_lx200gps   indi_pegasusindigo_wheel indi_simulator_rotator  indi_wanderer_lite_rotator
indi_astrotrac_telescope indi_giotto   indi_lx200pulsar2  indi_perfectstar_focus  indi_simulator_sqm  indi_wanderer_rotator_lite_v2
indi_baader_dome  indi_gpusb   indi_lx200ss2000pc  indi_pinefeat_cef_focus  indi_simulator_telescope indi_wanderer_rotator_mini
indi_camelot_rotator  indi_hid_test   indi_lx200zeq25   indi_planewave_deltat  indi_simulator_weather  indi_wandererbox_plus_v3
indi_celestron_gps  indi_hitecastrodc_focus  indi_lynx_focus   indi_planewave_telescope indi_simulator_wheel  indi_wandererbox_pro_v3
indi_celestron_sct_focus indi_ieaf_focus   indi_manual_wheel  indi_pmc8_telescope  indi_skycommander_telescope indi_wanderercover_v4_ec
indi_cheapodc   indi_ieq_telescope  indi_mbox_weather  indi_pyxis_rotator  indi_skysafari   indi_watchdog
indi_crux_mount   indi_ieqlegacy_telescope indi_meta_weather  indi_qhycfw1_wheel  indi_skywatcherAltAzMount indi_watcher_weather
indi_ddw_dome   indi_imager_agent  indi_microtouch_focus  indi_qhycfw2_wheel  indi_smartfocus_focus  indi_wavesharemodbus_relay
indi_deepskydad_af1_focus indi_integra_focus  indi_moonlite_focus  indi_qhycfw3_wheel  indi_snapcap   indi_weather_safety_alpaca
indi_deepskydad_af2_focus indi_ioptron_wheel  indi_moonlitedro_focus  indi_quantum_wheel  indi_spectracyber  indi_weather_safety_proxy
indi_deepskydad_af3_focus indi_ioptronHC8406  indi_mydcp4esp32  indi_rainbow_telescope  indi_sqm_weather  indi_weatherflow_weather
indi_deepskydad_fp  indi_ioptronv3_telescope indi_myDewControllerPro  indi_rainbowrsf_focus  indi_star2000   indi_xagyl_wheel
indi_deepskydad_fp1  indi_ipx800v4   indi_myfocuserpro2_focus indi_rbfocus_focus  indi_steeldrive_focus  indiserver
indi_deepskydad_fr1  indi_lacerta_mfoc_fmc_focus indi_nexdome_beaver  indi_rigel_dome   indi_steeldrive2_focus
indi_dmfc_focus   indi_lacerta_mfoc_focus  indi_nfocus   indi_robo_focus   indi_synscan_telescope
indi_domepro2_dome  indi_lakeside_focus  indi_nframe_rotator  indi_rolloff_dome  indi_synscanlegacy_telescope
indi_dragon_light  indi_lx200_10micron  indi_nightcrawler_focus  indi_scopedome_dome  indi_tcfs_focus
indi_dragonlair_dome  indi_lx200_16   indi_nstep_focus  indi_script_dome  indi_tcfs3_focus
```

Those are all the items we build in the compilation process. 

Before we go too far just create another terminal window, and make sure they are **both** in the same folder (use *pwd* to show you the folder) 

We are going to use 1 window to use the simulator, and the other to run kstars/ekos 

## Run the Simulator 

To see what we can run as a simulator we type 

     ls *simulator* 
     
```text
indi_simulator_ccd		indi_simulator_focus		indi_simulator_io		indi_simulator_rotator		indi_simulator_weather
indi_simulator_dome		indi_simulator_gps		indi_simulator_lightpanel	indi_simulator_sqm		indi_simulator_wheel
indi_simulator_dustcover	indi_simulator_guide		indi_simulator_receiver		indi_simulator_telescope
```

Quite an large choice - but we will start simple (!) 

  - Mount (referred to as a telescope) 
  - Main CCD
  - Guide Camera 

  
We just need to enter this command 

    ./indiserver indi_simulator_telescope indi_simulator_ccd indi_simulator_guide
    
    
Nothing exciting will happen - but you should see 

```text
2026-03-06T02:38:06: startup: indiserver indi_simulator_telescope indi_simulator_ccd indi_simulator_guide
2026-03-06T02:38:06: Driver indi_simulator_telescope: HaAxis: TrackRate 1, trackingRateDegSec 15.041067 arcsec
```

Our simulator is running.

## Start kstars 

We need to switch to the other terminal window, we should be in the same directory as the **indiserver** we just started previously. 

Now we just type 

    ./kstars 
    
Now there will be LOTS of messages/logging some of this can be useful if you are having issues. 

# Conclusion 

You are now running 

  - Indi simulators 
  - kstars 

Next phase will be to configure **Ekos** to use these 

This too is easy to accomplish.

The guide for (Ekos Simulator Setup)[./ekos_sim.md] will help you.