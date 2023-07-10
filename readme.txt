# ZASTRZEŻENIA
- choć nie powinny, mogą się zdarzyć błędy w składni, komendy są poglądowe pomagające sprecyzować wykonywane czynności i ich cele
- nic nie zastąpi umiejętności sprawnego posługiwania się dokumentacją oprogramowania, w którym każdy parametr i opcja jest dokładnie wytłumaczona wraz z przykładami i masoma przykładami na stackoverflow
- nie jest to poradnik słowo w słowo, gdyż nie zamierzam uczyć posługiwania się terminalem, bashem czy manem do rozwiązywania prostych problemów.
- wszelka dodatkowa bardziej mnie angażująca pomoc (czytaj więcej niż 2-3 pytania zajmujące mi ponad 5 minut) będzie kosztować extra za asystę

# HOW TO RESTORE IT
1. Musimy utworzyć woluminy dockerowe, na które przywrócimy nasze dane. Najlepiej po prostu odpalić config dockerowy z compose komendą 'docker-compose -f /path/to/file.yaml up -d', który sam utworzy woluminy, do których następnie się podepniemy w następnych etapach.
2. Jeśli w logach kontenerów nie ma podejrzanych błędów, możemy przejść do procesu przywrócenia backupu. W tym celu wyłączamy kontenery komendą 'docker-compose -f /path/to/file.yaml down'.
3. Następnie możemy przejść do procesu montowania woluminów i podmiany danych. Wpierw musimy wiedzieć, jakie woluminy nas interesują. Jako, że w zależności od konfiguracji przedrostek woluminu zmienia nazwę, najlepiej podglądnąć nazwę z użyciem komendy docker volume ls. Następnie woluminy porównujemy do nazw zawartych w configu yaml, powninno być na wzór <przedrostek>_<nazwa_woluminu_z_configu>. np. docker_openvpn-data.
4. Znając już woluminy przechodzimy do procesu montowania. Aby to zrobić, musimy uruchomić tymczasowy kontener pozwalający nam wykonać podmianę plików. W tym celu musimy podpiąć dwa woluminy. Pierwszy to będzie bind mount do ścieżki naszych danych z backupu, drugi zaś to będzie wolumin wykorzystywany przez naszą aplikacje. Robimy to za pomocą komendy docker run --rm -it -v ./backup/stunnel:/backup -v docker_stunnel-data:/replace alpine sh.
5. W ten sposób uruchamia się nam interaktywny kontener z shellem sh. Zauważ, że zastosowane woluminy /backup i /replace są zamontowane w tym kontenerze. Od teraz możesz za pomocą 'rm -rf /replace/*' usunąć wszystkie wygenerowane pliki i podmienić je za pomocą komendy 'cp -pr /backup/* /replace'. Analogicznie robisz z drugą porcją danych z backupu dla openvpn i woluminem openvpn. Parametr -v przyjmuje dwie rodzaje wartości:
- '-v /path/to/folder_or_file:/dir/in/container'
- '-v volume_name:/dir/in/container'