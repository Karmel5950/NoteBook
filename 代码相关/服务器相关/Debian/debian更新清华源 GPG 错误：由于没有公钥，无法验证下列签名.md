

apt-get update

正在读取软件包列表... 完成

W: GPG 错误：https://mirrors.tuna.tsinghua.edu.cn buster InRelease: 由于没有公钥，无法验证下列签名： NO_PUBKEY 04EE7237B7D453EC NO_PUBKEY 648ACFD622F3D138 NO_PUBKEY DCC9EFBF77E11517

W: GPG 错误：https://mirrors.tuna.tsinghua.edu.cn buster-updates InRelease: 由于没有公钥，无法验证下列签名： NO_PUBKEY 04EE7237B7D453EC NO_PUBKEY 648ACFD622F3D138

W: GPG 错误：https://mirrors.tuna.tsinghua.edu.cn buster-backports InRelease: 由于没有公钥，无法验证下列签名： NO_PUBKEY 04EE7237B7D453EC NO_PUBKEY 648ACFD622F3D138

W: GPG 错误：https://mirrors.tuna.tsinghua.edu.cn buster/updates InRelease: 由于没有公钥，无法验证下列签名： NO_PUBKEY AA8E81B4331F7F50 NO_PUBKEY 112695A0E562B32A

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7EA0A9C3F273FCD8##（此处换成错误的签名）