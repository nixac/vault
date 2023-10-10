> [!tip] Can't find what you need?
> For more generic setup guides not mentioned here, you may want to check [Bemani Guide](https://guide.fumo.photos)
> If you think something would fit more in here instead, you may [file a new issue](https://codeberg.org/nixac/pages/issues)

# Local EA server
>[!attention]
>This guide is only needed for self-hosted servers like [asphyxia](https://asphyxia-core.github.io), if using a private network you only need to create [`prop/linux.json`](/Devel/Config%20File%20Specification) as shown in [this example](/Devel/Config%20File%20Specification#Connection%20with%20ambiguous%20network)

>[!abstract] 　
>1. Download latest linux build of [asphyxia-core](https://github.com/asphyxia-core/asphyxia-core.github.io/releases/latest/download/asphyxia-core-linux-x64.zip)
>2. In this guide we assume you extracted asphyxia to `/var/games/iidx/server`
>3. Get plugins for game you need and copy to `/var/games/iidx/server`, so in case of iidx plugins it would look like `/var/games/iidx/server/plugins/iidx@asphyxia/`
>4. Launch asphyxia server in the background
>```shell
>/var/games/iidx/server/asphyxia-core -p 1108 -b localhost
>```
>5. You should be able to view apshyxia's control dashboard through your browser at `http://127.0.0.1:1108/`
>6. Create [config file](/Devel/Config%20File%20Specification) like in the example below
>![[Config File Specification#Connection with ambiguous network | no-h2]]
>7. Configure spicetools for carding-in - simplest way to do it is to launch spicetools config tool and press generate for 'Player1' in 'Cards' tab and bind a key for 'P1 Keypad Insert Card' in 'Buttons' tab (also 'Toggle Sub Screen' for games that support that)
>```shell
>ep_bm2dxnix 28 --cfg
>```
>8. If you launch game through ep_bm2dxnix now, game should connect with asphyxia's network and you should be able to card-in with button you bound in the previous step
>```shell
>ep_bm2dxnix 28
>```


# Fine-tuning pipewire audio
![[bmsound_wine#Advanced configuration|full no-h1 link-hover]]

# Adjusting settings

## Game
>[!abstract] 　
>1. Create file `linux.json` in your game's config dir, this would be `contents/prop/linux.json` for IIDX titles
>2. Check [this table](/Devel/Config%20File%20Specification#Valid%20keys) for configurable settings and define JSON document only with settings you need, if you don'y know how check [examples first](/Devel/Config%20File%20Specification#Common%20examples)
>3. Run any ep_bm2dxnix operation and you should notice your settings applied
>```shell
>ep_bm2dxnix 28
>```

## Launcher
![[ep_bm2dxnix#^0dc839]]
>[!info] User configurable paths
>![[ep_bm2dxnix#^03e3f7]]



