# wmt_tdeck_study
[WIP very much, many bugs] My LilyGo T-Deck (tdeck) and T-Deck Plus study, its MCU is ESP32-S3

## NOTE: Currently all projects are unstable and are with many bugs

## ESP-IDF version: 5.3

## How to build and burn firmware and view debug log
* idf.py build
* idf.py flash
* idf.py monitor

## Ref
* https://github.com/Xinyuan-LilyGO/T-Deck
* https://github.com/espressif/esp-idf/tree/release/v5.3/examples/peripherals/spi_master/lcd
* https://github.com/espressif/esp-idf/tree/release/v5.3/examples/peripherals/lcd/spi_lcd_touch
* https://github.com/espressif/esp-idf/tree/release/v5.3/examples/system/unit_test/components/testable
* https://github.com/espressif/esp-idf/tree/release/v5.3/examples/peripherals/lcd/tjpgd

## spi_lcd_touch changes
* Comparation
* (Before) https://github.com/espressif/esp-idf/blob/release/v5.3/examples/peripherals/lcd/spi_lcd_touch/main/spi_lcd_touch_example_main.c
* (After) https://github.com/weimingtom/wmt_tdeck_study/blob/master/spi_lcd_touch/main/spi_lcd_touch_example_main.c
* Adding
```
//! The board peripheral power control pin needs to be set to HIGH when using the peripheral
#define BOARD_POWERON       10
```
* Change
```
#define EXAMPLE_PIN_NUM_SCLK           40//18
#define EXAMPLE_PIN_NUM_MOSI           41//19
#define EXAMPLE_PIN_NUM_MISO           38//21
#define EXAMPLE_PIN_NUM_LCD_DC         11//5
#define EXAMPLE_PIN_NUM_LCD_RST        17//RADIO_RST_PIN==17, -1//3
#define EXAMPLE_PIN_NUM_LCD_CS         12//4
#define EXAMPLE_PIN_NUM_BK_LIGHT       42//2
#define EXAMPLE_PIN_NUM_TOUCH_CS       16//15

// The pixel number in horizontal and vertical
#if CONFIG_EXAMPLE_LCD_CONTROLLER_ILI9341
#define EXAMPLE_LCD_H_RES              240//320//
```
* Change
```
#if !defined(BOARD_POWERON)        
        esp_lcd_panel_mirror(panel_handle, true, false);
#else
        esp_lcd_panel_mirror(panel_handle, true, true);
#endif
```
* Adding
```

#if defined(BOARD_POWERON)
    //! The board peripheral power control pin needs to be set to HIGH when using the peripheral
    //pinMode(BOARD_POWERON, OUTPUT);
    //digitalWrite(BOARD_POWERON, HIGH);

    gpio_config_t io_conf = {};
    io_conf.pin_bit_mask = (1ULL << BOARD_POWERON);
    io_conf.mode = GPIO_MODE_OUTPUT;
    io_conf.pull_up_en = true;
    gpio_config(&io_conf);
    gpio_set_level(BOARD_POWERON, 1);
#endif    
```
* Change
```
#if 0
    ESP_LOGI(TAG, "Install ILI9341 panel driver");
    ESP_ERROR_CHECK(esp_lcd_new_panel_ili9341(io_handle, &panel_config, &panel_handle));
#else
    ESP_LOGI(TAG, "Install ST7789 panel driver");
    ESP_ERROR_CHECK(esp_lcd_new_panel_st7789(io_handle, &panel_config, &panel_handle));
#endif
```
* Change
```
#if !defined(BOARD_POWERON)
    ESP_ERROR_CHECK(esp_lcd_panel_mirror(panel_handle, true, false));
#else
    ESP_ERROR_CHECK(esp_lcd_panel_mirror(panel_handle, true, true));
#endif
```

## tdeck_esp32s3_unittest_v1.7z
* TOUCH_MODULES_GT911

