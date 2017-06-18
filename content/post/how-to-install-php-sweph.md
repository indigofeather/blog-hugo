---
title: 如何安裝 php-sweph
slug: how-to-install-php-sweph
date: 2014-05-09
tags:
- 程式筆記
- PHP
- Swiss Ephemeris
thumbnailImagePosition: left
thumbnailImage: /images/php.png
---

瑞士星曆（Swiss Ephemeris）是由 Astrodienst 開發的高精度星曆，其函式庫在各家占星軟體皆能見其蹤影。而 PHP 環境下也有包裝好的擴充套件，就讓我們先踏出第一步，完成開發環境。

<!--more-->

{{< image classes="fig-100" src="/images/php.png" >}}

## 環境
 - Ubuntu 14.04
 - Nginx + php-fpm
 - php 5.5.9

## 安裝

### lib（選擇性）
    sudo apt-get install -y re2c

### Swiss Ephemeris
    cd /tmp
    wget -nc http://www.astro.com/ftp/swisseph/swe_unix_src_2.00.00.tar.gz
    tar xzf swe_unix_src_2.00.00.tar.gz
    cd src
    make clean
    make libswe.a

複製 `libswe.a` 到 `/usr/lib/`

    sudo cp libswe.a /usr/lib/
    sudo chown root:root /usr/lib/libswe.a

複製 `sweodef.h` 和 `swephexp.h` 到 `/usr/include/`

    sudo cp sweodef.h swephexp.h /usr/include/
    sudo chown root:root /usr/include/sweodef.h
    sudo chown root:root /usr/include/swephexp.h

### php-sweph
    cd /tmp
    wget -nc https://php-sweph.googlecode.com/files/php-sweph-1.80.tgz
    tar xzf php-sweph-1.80.tgz
    cd php-sweph/
    phpize
    ./configure --enable-sweph
    make
    cd modules

複製 `sweph.so`，`20121212` 是版號日期，也許各環境不同，須注意。
偵測方法：

    ls /usr/lib/php5/ | grep '^[0-9]\+$'

執行：

    sudo cp sweph.so /usr/lib/php5/20121212/
    sudo chown root:root /usr/lib/php5/20121212/sweph.so

### Swiss Ephemeris 資料
    cd /tmp
    wget -nc http://www.astro.com/ftp/swisseph/ephe/archive_gzip/sweph_18.tar.gz
    tar xzf sweph_18.tar.gz
    sudo chown root:root *.se1
    sudo mkdir /usr/local/share/sweph
    sudo mv *.se1 /usr/local/share/sweph

`sweph_18.tar.gz` 資料範圍為 `1800 AD - 2400 AD`，不夠可以自己再多下載。

### 設定 PHP

建立 `sweph.ini`：

    cat << EOF >> sweph.ini
    extension=sweph.so
    EOF

    sudo chown root:root sweph.ini
    sudo mv sweph.ini /etc/php5/mods-available/

接著連結模組：

    cd /etc/php5/cli/conf.d
    sudo ln -sf ../../mods-available/sweph.ini sweph.ini
    cd /etc/php5/fpm/conf.d
    sudo ln -sf ../../mods-available/sweph.ini sweph.ini

重啟 PHP：

    sudo service php5-fpm restart

## 測試安裝結果

    php -i | grep sweph

    # 輸出：
    /etc/php5/cli/conf.d/sweph.ini
    sweph
    sweph support => enabled

## 測試程式

官網範例：

    <?php

        swe_set_ephe_path('/usr/local/share/sweph');

        list($y, $m, $d, $h, $mi, $s) = sscanf(gmdate("Y m d G i s"), "%d %d %d %d %d %d");
        $jul_ut = swe_julday($y, $m, $d, ($h + $mi / 60 + $s / 3600), SE_GREG_CAL);

        echo '<table>';
        echo "<tr><td colspan=5>Date: $y-$m-$d $h:$mi:$s</td></tr>";
        echo "<tr><td colspan=5>Julian Date: $jul_ut</td></tr>";

        for($i = SE_SUN; $i <= SE_VESTA; $i++)
        {
            if ($i == SE_EARTH) continue;

            echo '<tr>';
            echo '<td>' . swe_get_planet_name($i) . '</td>';

            $xx = swe_calc_ut($jul_ut, $i, SEFLG_SPEED);
            if ($xx['rc'] < 0) { // error calling swe_calc_ut();
                echo "<td colspan=4>" . $xx['serr'] . "</td>";
                continue;
            }
            echo '<td>' . $xx[0] . '</td>';
            echo '<td>' . $xx[1] . '</td>';
            echo '<td>' . $xx[2] . '</td>';
            echo '<td>' . $xx[3] . '</td>';
            echo '</tr>';
        }
        echo '</table>';

輸出：

<table><tbody><tr><td colspan="5">Date: 2014-5-8 17:31:49</td></tr><tr><td colspan="5">Julian Date: 2456786.2304282</td></tr><tr><td>Sun</td><td>48.046934485701</td><td>-0.00011030225421757</td><td>1.0093350060698</td><td>0.96720783634284</td></tr><tr><td>Moon</td><td>155.57453686241</td><td>-4.1863073388272</td><td>0.0026804469778759</td><td>12.055474065008</td></tr><tr><td>Mercury</td><td>62.161481194917</td><td>1.7100280613965</td><td>1.1772684856754</td><td>1.9273970278723</td></tr><tr><td>Venus</td><td>6.4639200236421</td><td>-1.6112124952661</td><td>1.0390746723741</td><td>1.1435593559941</td></tr><tr><td>Mars</td><td>189.86531321639</td><td>1.2627289488624</td><td>0.67163852976878</td><td>-0.14948157913919</td></tr><tr><td>Jupiter</td><td>106.17704661914</td><td>0.30918931614512</td><td>5.7013921581699</td><td>0.16316961506248</td></tr><tr><td>Saturn</td><td>230.17135468677</td><td>2.484312556999</td><td>8.9003216592223</td><td>-0.075141882322412</td></tr><tr><td>Uranus</td><td>14.459547319696</td><td>-0.64968928978702</td><td>20.857992775745</td><td>0.049733758674056</td></tr><tr><td>Neptune</td><td>337.31711618569</td><td>-0.69935948164465</td><td>30.293409172352</td><td>0.017091485593901</td></tr><tr><td>Pluto</td><td>283.44167876541</td><td>2.6628316895367</td><td>32.064051415535</td><td>-0.011429621070316</td></tr><tr><td>mean Node</td><td>207.50361447397</td><td>0</td><td>0.0025695552899546</td><td>-0.052975613790986</td></tr><tr><td>true Node</td><td>208.25106350773</td><td>0</td><td>0.00255274732235</td><td>0.019136034551145</td></tr><tr><td>mean Apogee</td><td>127.28524747603</td><td>-5.0709876075448</td><td>0.0027106251318856</td><td>0.11200829653211</td></tr><tr><td>osc. Apogee</td><td>125.11716931203</td><td>-5.2213685165675</td><td>0.0027051150540632</td><td>-1.786571638351</td></tr><tr><td>Chiron</td><td>346.94321313236</td><td>4.8243529423127</td><td>18.285516449326</td><td>0.03680008013925</td></tr><tr><td>Pholus</td><td>263.54904018498</td><td>16.092227116687</td><td>24.97393649368</td><td>-0.02727911506115</td></tr><tr><td>Ceres</td><td>200.44076098779</td><td>12.541445130447</td><td>1.7209741614103</td><td>-0.15378420382947</td></tr><tr><td>Pallas</td><td>148.22589320013</td><td>0.54839230609765</td><td>1.8840007581061</td><td>0.19969917610304</td></tr><tr><td>Juno</td><td>36.9631039348</td><td>-5.8400498205</td><td>3.0047686828182</td><td>0.57585482868684</td></tr><tr><td>Vesta</td><td>197.92508910831</td><td>11.778217062251</td><td>1.2816560060747</td><td>-0.15429845425954</td></tr></tbody></table>

## 結語
安裝完成只是第一步，接下來的開發應用才是更深遠的坑！
