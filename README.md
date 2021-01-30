# Установка arch linux 

## 1. Загружайте образ archiso с официального сайта, запишите на флешку командой:
```
# dd if=<путь до образа> of=/dev/sda status=progress
```
После записи образа, выключаете компьютер, меняете приоритет загрузки в биосе и загружаетесь в биос

## 2. Разметка диска:
```
# cfdisk
```
Вы находитесь в меню cfdisk, не пугайтесь, если бы пока не понимаете что делаете:

## 3. Форматирование разделов 
ВНИМАНИЕ! ПОСЛЕ ВЫПОЛНЕНИЯ ДАННОГО ПУНКА ВСЯ ИНФОРМАЦИЯ НА ДИСКЕ БУДЕТ УТЕРЯНА!
```
# mkfs.ext4 /dev/sda1 -L boor
# mkswap /dev/sda2 -L swap
# mkfs.ext4 /dev/sda3 -L root 
# mkfs.ext4 /dev/sda4 -L home 
```

## 4. Монтирование разделов
```
# mount /dev/sda3 /mnt 
# mkdir /mnt/boot && mkdir /mnt/home
# mount /dev/sda1 /mnt/boot
# mount /dev/sda4 /mnt/home
```

### Подключим раздел подкачки(swap) 
```
# swapon /dev/sda2 
```

## 5. Подключение к wifi:
```
# iwctl
# station list (Смотрим название нашего модуля, запоминаем его, самые распространненые - wlan0 или wlp3sp
# station <имя модуля> connect <имя вашего wifi>
```
Если у вас все ок, переходите дальше

## Установка пакетов
```
# pacstrap /mnt base base-devel networkmanager nano network-manager-applet grub linux linux-headers mkinitcpio git dkms
```

## 8. Первичная настройка системы Сгенерируем fstab, для этого используем следующую команду: 
```
# genfstab -p /mnt >> /mnt/etc/fstab 
```

## 9. Перейдём в установленную систему: 
```
# arch-chroot /mnt 
```

## 10. Добавим русскую локаль в систему: 

```
# nano /etc/locale.gen 
```
## В файле /etc/locale.gen раскомментируйте(уберите # вначале) строки :
```
#en_US.UTF-8 UTF-8 
#ru_RU.UTF-8 UTF-8 
```

## 11. Обновим текущую локаль системы:
```
# locale-gen 
```

## 12. Добавим русскую локаль в консоль: Откройте файл /etc/mkinitcpio.conf: 
```
# nano /etc/mkinitcpio.conf 
```

### В разделе HOOKS, должен быть прописан хук keymap. Создадим загрузочный RAM диск: 
```
# mkinitcpio -p linux 
```

## 13. Установим grub:
```
# grub-install --target=i386-pc /dev/sda --force
```

### Обновим grub.cfg: 
```
# grub-mkconfig -o /boot/grub/grub.cfg 
```

## 14. Установим root пароль: 
```
# passwd
```

## 15.  Выйдем из установленной системы: 
```
# exit
```

## 16. Перезагрузка
```
# reboot 
```

## 17. После успешной перезагрузки:
И нужно зайти в root пользователя с помощью ранее введённого пароля. Изменим имя компьютера(замените myhostname на своё):
```
# hostnamectl set-hostname myhostname
```

## 18. Установим временную зону: 
```
# timedatectl set-timezone Europe/Moscow 
```

## 19. Локализуем систему: 

```
# setfont cyr-sun16 
# localectl set-locale LANG="ru_RU.UTF-8" 
# export LANG=ru_RU.UTF-8 
# echo 'FONT=cyr-sun16' > /etc/vconsole.conf. 
```

## 20. Обновим загрузочный RAM диск: 
```
# mkinitcpio -p linux
```

## 21. Обновим grub.cfg(для локализации): 
```
# grub-mkconfig -o /boot/grub/grub.cfg 
```

## 22. Настроим pacman (только для x86_64): 
```
# nano /etc/pacman.conf 
```

Для работы 32-битных приложений в 64-битной системе нужно раскомментировать (удалить # вначале) репозиторий multilib:
```
#[multilib] #Include = /etc/pacman.d/mirrorlist
```

## 23.  Добавим пользователя (замените myusername на своё):

```
# useradd -m -g users -G wheel -s /bin/bash myusername 
```

### Установим ему пароль: 
```
# passwd myusername 
```

## 24. Включим NetworkManager
```
# systemctl start NetworkManager 
# systemctl enable NetworkManager 
```

## 25. Обновим базы данных пакетов: 
```
# pacman -Syy
```
## 26. Настроим sudo EDITOR=nano visudo:
### Раскомментируем строку 
```
# %wheel ALL=(ALL) ALL
```
Теперь мы можем использовать sudo для выполнения команд администратора.
 
## 27. Установка "yay" из AUR: 
```
$ cd && git clone https://aur.archlinux.org/yay.git
$ whoami
```
### Если после whoami, вывело не root, пишите:
```
makepkg -si
```

### Если вы после этой командой видите root, то перейдите к 23 пункту. Если вы его выполнили пишите:
```
exit
[*Введите логин, который вы вводили в пункте 23*]
[*Введите пароль*]

$ makepkg -si 
```

## 28. Установка zsh:
```
$ sudo pacman -S zsh
$ sudo pacman -S wget 
$ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

## 29. Установка zsh по умолчанию:
```
$ chsh -s /bin/zsh user 
```

## 30. Общие рекомендации:

- *Часто обновляйте Arch!*

- *Не выносите мозг остальным, зайдите в документацию, в 99.9% случаях все уже есть.*
 
- *Если вы не понимаете, зачем Вам Arch Linux - сносите, он вам ненужен.*
