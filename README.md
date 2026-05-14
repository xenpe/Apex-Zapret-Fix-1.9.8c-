# Apex-Zapret-Fix-1.9.8c-
Я тут надумал вот что: без запрета Арех пашет, с ним нет. Сделал так, что запрет трогает только ДС, Ютуб и домены в list genera, list google и ipset all. Создал батник свой в папке с запретом, чтобы не расписывать вручную все текстовые листы. Чтобы не возиться с файлами в папке list, просто закиньте этот батник в основную папку запрета и все. Ну если не хотите скачиваить избегая риски, то вот конфиг:
@echo off
chcp 65001 > nul

cd /d "%~dp0"
call service.bat status_zapret
call service.bat check_updates
call service.bat load_game_filter
echo:

set "BIN=%~dp0bin\"
set "LISTS=%~dp0lists\"
cd /d %BIN%

taskkill /f /im winws.exe >nul 2>&1

:: СТАРТ
:: 1. Сначала ИГРЫ — ставим их в начало и ПРЯМО говорим не трогать (cutoff=n0)
:: 2. Потом DISCORD и YOUTUBE по их рабочим схемам
start "zapret: FINAL_HYBRID_WORKING" /min "%BIN%winws.exe" --wf-tcp=80,443,2053,2083,2087,2096,8443,%GameFilterTCP% --wf-udp=443,19294-19344,50000-50100,%GameFilterUDP% ^
--filter-tcp=%GameFilterTCP% --dpi-desync=fake --dpi-desync-cutoff=n0 --new ^
--filter-udp=%GameFilterUDP% --dpi-desync=fake --dpi-desync-cutoff=n0 --new ^
--filter-udp=443 --hostlist="%LISTS%list-general.txt" --hostlist="%LISTS%list-general-user.txt" --dpi-desync=fake --dpi-desync-repeats=11 --dpi-desync-fake-quic="%BIN%quic_initial_www_google_com.bin" --new ^
--filter-udp=19294-19344,50000-50100 --filter-l7=discord,stun --dpi-desync=fake --dpi-desync-fake-discord="%BIN%quic_initial_dbankcloud_ru.bin" --dpi-desync-fake-stun="%BIN%quic_initial_dbankcloud_ru.bin" --dpi-desync-repeats=6 --new ^
--filter-tcp=2053,2083,2087,2096,8443 --hostlist-domains=discord.media --dpi-desync=fake,multisplit --dpi-desync-split-seqovl=681 --dpi-desync-split-pos=1 --dpi-desync-fooling=ts --dpi-desync-repeats=8 --dpi-desync-fake-tls-mod=rnd,dupsid,sni=www.google.com --new ^
--filter-tcp=443 --hostlist="%LISTS%list-google.txt" --ip-id=zero --dpi-desync=fake,multisplit --dpi-desync-split-seqovl=681 --dpi-desync-split-pos=1 --dpi-desync-fooling=ts --dpi-desync-repeats=8 --dpi-desync-split-seqovl-pattern="%BIN%tls_clienthello_www_google_com.bin" --dpi-desync-fake-tls="%BIN%tls_clienthello_www_google_com.bin" --new ^
--filter-tcp=80,443 --hostlist="%LISTS%list-general.txt" --hostlist="%LISTS%list-google.txt" --dpi-desync=fake,multisplit --dpi-desync-split-seqovl=681 --dpi-desync-split-pos=1 --dpi-desync-fooling=ts --dpi-desync-repeats=8 --dpi-desync-fake-tls-mod=rnd,dupsid,sni=www.google.com --dpi-desync-fake-http="%BIN%tls_clienthello_max_ru.bin"
