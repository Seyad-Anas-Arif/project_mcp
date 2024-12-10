# Compilation Instructions:
Source your environment:
$ . /opt/tdx-xwayland/6.6.0/environment-setup-cortexa53-tdx-linux

Compile UI and header files:
$ uic mainwindow.ui -o ui_mainwindow.h
$ moc mainwindow.h -o moc_mainwindow.cpp

Add necessary libraries used in the program:
$ CXXFLAGS="-I. \
         -I/opt/tdx-xwayland/6.6.0/sysroots/cortexa53-tdx-linux/usr/include/QtCore \
         -I/opt/tdx-xwayland/6.6.0/sysroots/cortexa53-tdx-linux/usr/include/QtGui \
         -I/opt/tdx-xwayland/6.6.0/sysroots/cortexa53-tdx-linux/usr/include/QtWidgets \
         -DQT_NO_DEBUG -DQT_WIDGETS_LIB -DQT_GUI_LIB -DQT_CORE_LIB"
$ LDFLAGS="-L/opt/tdx-xwayland/6.6.0/sysroots/cortexa53-tdx-linux/usr/lib \
        -lQt5Widgets -lQt5Gui -lQt5Core -lpthread"

Compile all source files:
$ $CXX $CXXFLAGS -c main.cpp -o main.o
$ $CXX $CXXFLAGS -c mainwindow.cpp -o mainwindow.o
$ $CXX $CXXFLAGS -c moc_mainwindow.cpp -o moc_mainwindow.o

Link into the final executable:
$ $CXX main.o mainwindow.o moc_mainwindow.o -o td_v4 $LDFLAGS

Copy the binary file to the target system:
$ scp -r td_v4 root@verdin-imx8mp-15010457.local:/home/root/testing_field/

# Setting up MCP Panel as a Default Application Using Systemd:

Create a myapp.service file on the target system:
root@verdin-imx8mp-15010457:~# cat /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=graphical.target

[Service]
ExecStart=/home/root/testing_field/td_v4
WorkingDirectory=/home/root/testing_field
Restart=always
User=root
Group=root
Environment=DISPLAY=:0
RestartSec=2

[Install]
WantedBy=default.target

Enable and start the service:
$ systemctl daemon-reload
$ systemctl enable myapp.service
$ systemctl start myapp.service
