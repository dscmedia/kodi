# -*- coding: utf-8 -*-

import sys
try:
    import cookielib
except ImportError:
    import http.cookiejar as cookielib
try:
    import urllib.parse as urllib
except ImportError:
    import urllib
try:
    import urllib2
except ImportError:
    import urllib.request as urllib2
import datetime
from datetime import datetime
import re
import os
import base64
import codecs
import xbmc
import xbmcplugin
import xbmcgui
import xbmcaddon
import xbmcvfs
import traceback
import time
import resolveurl

try:
    import json
except:
    import simplejson as json


##CONFIGURAÇÕES
####  TITULO DO MENU  #################################################################
title_menu = ''
###  DESCRIÇÃO DO ADDON ###############################################################
title_descricao = 'Descrição do addon'

####  LINK DO TITULO DE MENU  #########################################################
## OBS: POR PADRÃO JÁ TEM UM MENU EM BRANCO PARA NÃO TER ERRO AO CLICAR ###############
url_b64_title = ''
#url_title = base64.b64decode(url_b64_title).decode('utf-8')
url_title = ''


##### PESQUISA - get.php
#url_b64_pesquisa = ''
#url_pesquisa = base64.b64decode(url_b64_pesquisa).decode('utf-8')
url_pesquisa = 'http://teste.com/get.php'
menu_pesquisar = '[COLOR white][B]PESQUISAR...[/B][/COLOR]'
thumb_pesquisar = ''
fanart_pesquisar = ''
#### Descrição Pesquisa
desc_pesquisa = 'Pesquise por filme'
## MENU CONFIGURAÇÕES
menu_configuracoes = '[B][COLOR white]CONFIGURAÇÕES[/COLOR][/B]'
thumb_icon_config = ''
desc_configuracoes = ''
## FAVORITOS
menu_favoritos = '[B][COLOR white]FAVORITOS[/COLOR][/B]'
thumb_favoritos = ''
desc_favoritos = ''

#### MENU VIP ################################################################
titulo_vip = '[COLOR white][B]ÁREA DE ACESSO[/B][/COLOR] [COLOR gold][B](VIP)[/B][/COLOR]'
thumbnail_vip = ''
fanart_vip = ''
#### DESCRIÇÃO VIP ###########################################################
vip_descricao = ''
#### DIALOGO VIP - SERVIDOR DESATIVADO - CLICK ###################################
vip_dialogo = 'No Kodi só canais pelo vip, use o app'
##SERIVODR VIP
url_server_vip = ''


## MULTLINK
## nome para $nome, padrão: lsname para $lsname
playlist_command = 'nome'
dialog_playlist = 'Selecione um item'


# user agent - Padrão: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36
useragent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36'

# Base - seu link principal
url_b64_principal = 'url base 64 encode'
url_principal = ''

#name - mensagem suporte
addon_name = xbmcaddon.Addon().getAddonInfo('name')


if sys.argv[1] == 'limparFavoritos':
    Path = xbmc.translatePath(xbmcaddon.Addon().getAddonInfo('profile')).decode("utf-8")
    arquivo = os.path.join(Path, "favorites.dat")
    exists = os.path.isfile(arquivo)
    if exists:
        try:
            os.remove(arquivo)
        except:
            pass
    xbmcgui.Dialog().ok('Sucesso', '[B][COLOR red]Favoritos limpo com sucesso![/COLOR][/B]')
    xbmc.sleep(2000)
    exit()


if sys.argv[1] == 'SetPassword':
    addonID = xbmcaddon.Addon().getAddonInfo('id')
    addon_data_path = xbmc.translatePath(os.path.join('special://home/userdata/addon_data', addonID))
    if os.path.exists(addon_data_path)==False:
        os.mkdir(addon_data_path)
    xbmc.sleep(4)
    #Path = xbmc.translatePath(xbmcaddon.Addon().getAddonInfo('profile')).decode("utf-8")
    #arquivo = os.path.join(Path, "password.txt")
    arquivo = os.path.join(addon_data_path, "password.txt")
    exists = os.path.isfile(arquivo)
    keyboard = xbmcaddon.Addon().getSetting("keyboard")
    if exists == False:
        password = '0069'
        p_encoded = base64.b64encode(password.encode()).decode('utf-8')
        p_file1 = open(arquivo,'w')
        p_file1.write(p_encoded)
        p_file1.close()
        xbmc.sleep(4)
        p_file = open(arquivo,'r+')
        p_file_read = p_file.read()
        p_file_b64_decode = base64.b64decode(p_file_read).decode('utf-8')
        dialog = xbmcgui.Dialog()
        if int(keyboard) == 0:
            ps = dialog.numeric(0, 'Insira a senha atual:')
        else:
            ps = dialog.input('Insira a senha atual:', option=xbmcgui.ALPHANUM_HIDE_INPUT)
        if ps == p_file_b64_decode:
            if int(keyboard) == 0:
                ps2 = dialog.numeric(0, 'Insira a nova senha:')
            else:
                ps2 = dialog.input('Insira a senha atual:', option=xbmcgui.ALPHANUM_HIDE_INPUT)
            if ps2 != '':
                ps2_b64 = base64.b64encode(ps2.encode()).decode('utf-8')
                p_file = open(arquivo,'w')
                p_file.write(ps2_b64)
                p_file.close()
                xbmcgui.Dialog().ok('[B][COLOR white]AVISO[/COLOR][/B]','A Senha foi alterada com sucesso!')
            else:
                xbmcgui.Dialog().ok('[B][COLOR white]AVISO[/COLOR][/B]','Não foi possivel alterar a senha!')
        else:
            xbmcgui.Dialog().ok('[B][COLOR white]AVISO[/COLOR][/B]','Senha invalida!, se não alterou utilize a senha padrão')
    else:
        p_file = open(arquivo,'r+')
        p_file_read = p_file.read()
        p_file_b64_decode = base64.b64decode(p_file_read).decode('utf-8')
        dialog = xbmcgui.Dialog()
        if int(keyboard) == 0:
            ps = dialog.numeric(0, 'Insira a senha atual:')
        else:
            ps = dialog.input('Insira a senha atual:', option=xbmcgui.ALPHANUM_HIDE_INPUT)
        if ps == p_file_b64_decode:
            if int(keyboard) == 0:
                ps2 = dialog.numeric(0, 'Insira a nova senha:')
            else:
                ps2 = dialog.input('Insira a senha atual:', option=xbmcgui.ALPHANUM_HIDE_INPUT)
            if ps2 != '':
                ps2_b64 = base64.b64encode(ps2.encode()).decode('utf-8')
                p_file = open(arquivo,'w')
                p_file.write(ps2_b64)
                p_file.close()
                xbmcgui.Dialog().ok('[B][COLOR white]AVISO[/COLOR][/B]','A Senha foi alterada com sucesso!')
            else:
                xbmcgui.Dialog().ok('[B][COLOR white]AVISO[/COLOR][/B]','Não foi possivel alterar a senha!')
        else:
            xbmcgui.Dialog().ok('[B][COLOR white]AVISO[/COLOR][/B]','Senha invalida!, se não alterou utilize a senha padrão')
    exit()



addon_handle = int(sys.argv[1])
__addon__ = xbmcaddon.Addon()
addon = __addon__
__addonname__ = __addon__.getAddonInfo('name')
__icon__ = __addon__.getAddonInfo('icon')
addon_version = __addon__.getAddonInfo('version')

    







def notify(message, timeShown=5000):
    xbmc.executebuiltin('Notification(%s, %s, %d, %s)' % (__addonname__, message, timeShown, __icon__))

def to_unicode(text, encoding='utf-8', errors='strict'):
    """Force text to unicode"""
    if isinstance(text, bytes):
        return text.decode(encoding, errors=errors)
    return text

def get_search_string(heading='', message=''):
    """Ask the user for a search string"""
    search_string = None
    keyboard = xbmc.Keyboard(message, heading)
    keyboard.doModal()
    if keyboard.isConfirmed():
        search_string = to_unicode(keyboard.getText())
    return search_string

def getRequest(url, count):
    proxy_mode = addon.getSetting('proxy')
    if proxy_mode == 'true':
        try:
            import requests
            import random
            headers={'User-agent': useragent,
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
            'Content-Type': 'text/html'}
            if int(count) > 0:
                attempt = int(count)-1
            else:
                attempt = 0
            #print('tentativa: '+str(attempt)+'')
            ### https://proxyscrape.com/free-proxy-list
            ##http
            data_proxy1 = getRequest2('https://api.proxyscrape.com/?request=getproxies&proxytype=http&timeout=10000&country=BR&ssl=no&anonymity=all', '')
            list1 = data_proxy1.splitlines()
            total1 = len(list1)
            number_http = random.randint(0,total1-1)
            proxy_http = 'http://'+list1[number_http]
            ##https
            data_proxy2 = getRequest2('https://api.proxyscrape.com/?request=getproxies&proxytype=http&timeout=10000&country=BR&ssl=yes&anonymity=all', '')
            list2 = data_proxy2.splitlines()
            total2 = len(list2)
            number_https = random.randint(0,total2-1)
            proxy_https = 'https://'+list2[number_https]
            #print(proxy_https)
            proxyDict = {"http" : proxy_http, "https" : proxy_https}
            req = requests.get(url, headers=headers, proxies=proxyDict)
            req.encoding = 'utf-8'
            #status = req.status_code
            response = req.text
            return response
        except:
            proxy_number = addon.getSetting('proxy_try')
            if int(attempt) > 0:
                limit = int(attempt)
            elif int(count) == 1 and int(attempt) == 0:
                limit = int(proxy_number)+1+1
            if int(limit) > 1:
                #print('ativar outro proxy')
                data = getRequest(url, int(limit))
                return data
            else:
                notify('[COLOR red]Erro ao utilizar o proxy ou servidor![/COLOR]')
                response = ''
                return response
    else:
        try:
            try:
                import urllib.request as urllib2
            except ImportError:
                import urllib2
            request_headers = {
            "Accept-Language": "pt-BR,pt;q=0.9,en;q=0.8,ru;q=0.7,de-DE;q=0.6,de;q=0.5,de-AT;q=0.4,de-CH;q=0.3,ja;q=0.2,zh-CN;q=0.1,zh;q=0.1,zh-TW;q=0.1,es;q=0.1,ar;q=0.1,en-GB;q=0.1,hi;q=0.1,cs;q=0.1,el;q=0.1,he;q=0.1,en-US;q=0.1",
            "User-Agent": useragent,
            "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9"
            }
            request = urllib2.Request(url, headers=request_headers)
            response = urllib2.urlopen(request).read().decode('utf-8')
            return response
        except urllib2.URLError as e:
            if hasattr(e, 'code'):
                xbmc.executebuiltin("XBMC.Notification(Falha, código de erro - "+str(e.code)+",10000,"+__icon__+")")    
            elif hasattr(e, 'reason'):
                xbmc.executebuiltin("XBMC.Notification(Falha, motivo - "+str(e.reason)+",10000,"+__icon__+")")
            response = ''
            return response



def getRequest2(url,ref,userargent=False):
    try:
        if ref > '':
            ref2 = ref
        else:
            ref2 = url
        if userargent:
            client_user = userargent
        else:
            client_user = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36'
        cj = cookielib.CookieJar()
        opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cj))
        opener.addheaders=[('Accept-Language', 'pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7'),('User-Agent', client_user),('Accept', 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9'), ('Referer', ref2)]
        data = opener.open(url).read()
        response = data.decode('utf-8')
        return response
    except:
        pass

def regex_get_all(text, start_with, end_with):
    r = re.findall("(?i)(" + start_with + "[\S\s]+?" + end_with + ")", text)
    return r



def re_me(data, re_patten):
    match = ''
    m = re.search(re_patten, data)
    if m != None:
        match = m.group(1)
    else:
        match = ''
    return match



def resolve_data(url):
    try:
        data = getRequest(url, 1)
        import gzip, binascii
        #k = base64.b32decode('').decode('utf-8')
        k = base64.b32decode('').decode('utf-8')
        try:
            from StringIO import StringIO as BytesIO ## for Python 2
        except ImportError:            
            from io import BytesIO ## for Python 3
        if k in data:
            data = data.split(k)
            buf = BytesIO(binascii.unhexlify(data[0]))
            f = gzip.GzipFile(fileobj=buf)
            data = f.read()
    except:
        data = getRequest(url, 1)        
    return data

def getData(url,fanart,pesquisa=False):
    adult = xbmcaddon.Addon().getSetting("adult")
    adult2 = xbmcaddon.Addon().getSetting("adult2")
    uhdtv = addon.getSetting('uhdtv')
    fhdtv = addon.getSetting('fhdtv')
    hdtv = addon.getSetting('hdtv')
    sdtv = addon.getSetting('sdtv')
    filtrar = addon.getSetting('filtrar')
    data = resolve_data(url)
    if isinstance(data, (int, str, list)):
        channels = re.compile('<channels>(.+?)</channels>',re.MULTILINE|re.DOTALL).findall(data)
        channel = re.compile('<channel>(.*?)</channel>',re.MULTILINE|re.DOTALL).findall(data)
        item = re.compile('<item>(.*?)</item>',re.MULTILINE|re.DOTALL).findall(data)
        if len(channels) >0:
            for channel in channel:
                linkedUrl=''
                lcount=0
                try:
                    linkedUrl = re.compile('<externallink>(.*?)</externallink>').findall(channel)[0]
                    lcount=len(re.compile('<externallink>(.*?)</externallink>').findall(channel))
                except: pass
                
                try:
                    linkedUrl = base64.b32decode(linkedUrl).decode('utf-8')
                except:
                    pass
                    

                name = re.compile('<name>(.*?)</name>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                try:
                    thumbnail = re.compile('<thumbnail>(.*?)</thumbnail>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                except:
                    thumbnail = ''
                try:
                    fanart1 = re.compile('<fanart>(.*?)</fanart>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                except:
                    fanart1 = ''

                if not fanart1:
                    if __addon__.getSetting('use_thumb') == "true":
                        fanArt = thumbnail
                    else:
                        fanArt = fanart
                else:
                    fanArt = fanart1
                if fanArt == None:
                    #raise
                    fanArt = ''

                try:
                    desc = re.compile('<info>(.*?)</info>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if desc == None:
                        #raise
                        desc = ''
                except:
                    desc = ''

                try:
                    genre = re.compile('<genre>(.*?)</genre>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if genre == None:
                        #raise
                        genre = ''
                except:
                    genre = ''

                try:
                    date = re.compile('<date>(.*?)</date>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if date == None:
                        #raise
                        date = ''
                except:
                    date = ''

                try:
                    credits = re.compile('<credits>(.*?)</credits>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if credits == None:
                        #raise
                        credits = ''
                except:
                    credits = ''

                try:
                    year = re.compile('<year>(.*?)</year>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if year == None:
                        #raise
                        year = ''
                except:
                    year = ''

                try:
                    director = re.compile('<director>(.*?)</director>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if director == None:
                        #raise
                        director = ''
                except:
                    director = ''

                try:
                    writer = re.compile('<writer>(.*?)</writer>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if writer == None:
                        #raise
                        writer = ''
                except:
                    writer = ''

                try:
                    duration = re.compile('<duration>(.*?)</duration>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if duration == None:
                        #raise
                        duration = ''
                except:
                    duration = ''

                try:
                    premiered = re.compile('<premiered>(.*?)</premiered>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if premiered == None:
                        #raise
                        premiered = ''
                except:
                    premiered = ''

                try:
                    studio = re.compile('<studio>(.*?)</studio>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if studio == None:
                        #raise
                        studio = ''
                except:
                    studio = ''

                try:
                    rate = re.compile('<rate>(.*?)</rate>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if rate == None:
                        #raise
                        rate = ''
                except:
                    rate = ''

                try:
                    originaltitle = re.compile('<originaltitle>(.*?)</originaltitle>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if originaltitle == None:
                        #raise
                        originaltitle = ''
                except:
                    originaltitle = ''

                try:
                    country = re.compile('<country>(.*?)</country>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if country == None:
                        #raise
                        country = ''
                except:
                    country = ''

                try:
                    rating = re.compile('<country>(.*?)</country>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if rating == None:
                        #raise
                        rating = ''
                except:
                    rating = ''

                try:
                    userrating = re.compile('<userrating>(.*?)</userrating>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if userrating == None:
                        #raise
                        userrating = ''
                except:
                    userrating = ''

                try:
                    votes = re.compile('<votes>(.*?)</votes>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if votes == None:
                        #raise
                        votes = ''
                except:
                    votes = ''

                try:
                    aired = re.compile('<aired>(.*?)</aired>',re.MULTILINE|re.DOTALL).findall(channel)[0]
                    if aired == None:
                        #raise
                        aired = ''
                except:
                    aired = ''

                try:
                    if linkedUrl=='':
                        #addDir(name.encode('utf-8', 'ignore'),url.encode('utf-8'),2,thumbnail,fanArt,desc,genre,date,credits,True)
                        #addDir(name.encode('utf-8', 'ignore'),url.encode('utf-8'),2,thumbnail,fanArt,desc,genre,date,credits,year,director,writer,duration,premiered,studio,rate,originaltitle,country,rating,userrating,votes,aired)
                        addDir(name.encode('utf-8', 'ignore'),'',1,thumbnail,fanArt,desc,genre,date,credits,year,director,writer,duration,premiered,studio,rate,originaltitle,country,rating,userrating,votes,aired)
                    else:
                        #print linkedUrl
                        #addDir(name.encode('utf-8'),linkedUrl.encode('utf-8'),1,thumbnail,fanArt,desc,genre,date,None,'source')
                        if adult == 'false' and re.search("ADULTOS",name,re.IGNORECASE) and name.find('(+18)') >=0:
                            pass
                        else:
                            addDir(name.encode('utf-8', 'ignore'),linkedUrl,1,thumbnail,fanArt,desc,genre,date,credits,year,director,writer,duration,premiered,studio,rate,originaltitle,country,rating,userrating,votes,aired)
                except:
                    notify('[COLOR red]Erro ao Carregar os dados![/COLOR]')
        elif re.search("#EXTM3U",data) or re.search("#EXTINF",data):
            content = data.rstrip()
            match = re.compile(r'#EXTINF:(.+?),(.*?)[\n\r]+([^\r\n]+)').findall(content)
            for other,channel_name,stream_url in match:
                if 'tvg-logo' in other:
                    thumbnail = re_me(other,'tvg-logo=[\'"](.*?)[\'"]')
                    if thumbnail:
                        if thumbnail.startswith('http'):
                            thumbnail = thumbnail
                        #elif not addon.getSetting('logo-folderPath') == "":
                        #    logo_url = addon.getSetting('logo-folderPath')
                        #    thumbnail = logo_url + thumbnail

                        else:
                            thumbnail = ''
                    else:
                        thumbnail = ''
                else:
                    thumbnail = ''

                if 'group-title' in other:
                    cat = re_me(other,'group-title=[\'"](.*?)[\'"]')
                else:
                    cat = ''

                try:
                    #resolver_final = resolver(stream_url, channel_name, thumbnail)
                    if uhdtv == 'false' and re.search("4K",channel_name):
                        pass
                    elif fhdtv == 'false' and re.search("FHD",channel_name):
                        pass
                    elif hdtv == 'false' and re.search("HD",channel_name) and not re.search("FHD",channel_name):
                        pass
                    elif sdtv == 'false' and re.search("SD",channel_name):
                        pass
                    elif sdtv == 'false' and not re.search("SD",channel_name) and not re.search("HD",channel_name) and not re.search("4K",channel_name):
                        pass
                    #Futebol
                    elif int(filtrar) == 1 and re.search("Praia",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 1 and not re.search("SPORTV",channel_name,re.IGNORECASE) and not re.search("DAZN",channel_name,re.IGNORECASE) and not re.search("ESPN Brasil",channel_name,re.IGNORECASE) and not re.search("PREMIERE",channel_name,re.IGNORECASE) and not re.search("COPA",channel_name,re.IGNORECASE):
                        pass
                    #Esportes
                    elif int(filtrar) == 2 and re.search("Praia",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 2 and not re.search("Band Sports",channel_name,re.IGNORECASE) and not re.search("Combate",channel_name,re.IGNORECASE) and not re.search("Fox Sports",channel_name,re.IGNORECASE) and not re.search("SPORTV",channel_name,re.IGNORECASE) and not re.search("DAZN",channel_name,re.IGNORECASE) and not re.search("ESPN",channel_name,re.IGNORECASE) and not re.search("PREMIERE",channel_name,re.IGNORECASE) and not re.search("COPA",channel_name,re.IGNORECASE):
                        pass
                    #Filmes e Series
                    elif int(filtrar) == 3 and re.search("Sports",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 3 and re.search("XY Max",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 3 and not re.search("AMC",channel_name,re.IGNORECASE) and not re.search("Canal Brasil",channel_name,re.IGNORECASE) and not re.search("Cinemax",channel_name,re.IGNORECASE) and not re.search("HBO",channel_name,re.IGNORECASE) and not re.search("Max",channel_name,re.IGNORECASE) and not re.search("Megapix",channel_name,re.IGNORECASE) and not re.search("Paramount",channel_name,re.IGNORECASE) and not re.search("SPACE",channel_name,re.IGNORECASE) and not re.search("TCM",channel_name,re.IGNORECASE) and not re.search("Telecine Action",channel_name,re.IGNORECASE) and not re.search("TC Action",channel_name,re.IGNORECASE) and not re.search("Telecine Cult",channel_name,re.IGNORECASE) and not re.search("TC Cult",channel_name,re.IGNORECASE) and not re.search("TC Cult",channel_name,re.IGNORECASE) and not re.search("Telecine Fun",channel_name,re.IGNORECASE) and not re.search("TC Fun",channel_name,re.IGNORECASE) and not re.search("Telecine Pipoca",channel_name,re.IGNORECASE) and not re.search("TC Pipoca",channel_name,re.IGNORECASE) and not re.search("Telecine Premium",channel_name,re.IGNORECASE) and not re.search("TC Premium",channel_name,re.IGNORECASE) and not re.search("Telecine Touch",channel_name,re.IGNORECASE) and not re.search("TC Touch",channel_name,re.IGNORECASE) and not re.search("TNT",channel_name,re.IGNORECASE) and not re.search("A&E",channel_name,re.IGNORECASE) and not re.search("AXN",channel_name,re.IGNORECASE) and not re.search("AXN",channel_name,re.IGNORECASE) and not re.search("FOX",channel_name,re.IGNORECASE) and not re.search("FX",channel_name,re.IGNORECASE) and not re.search("SONY",channel_name,re.IGNORECASE) and not re.search("Studio Universal",channel_name,re.IGNORECASE) and not re.search("SyFy",channel_name,re.IGNORECASE) and not re.search("Universal Channel",channel_name,re.IGNORECASE) and not re.search("Universal TV",channel_name,re.IGNORECASE) and not re.search("Warner",channel_name,re.IGNORECASE):
                        pass
                    #Infantil
                    elif int(filtrar) == 4 and re.search("FM",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 4 and not re.search("Baby TV",channel_name,re.IGNORECASE) and not re.search("BOOMERANG",channel_name,re.IGNORECASE) and not re.search("CARTOON NETWORK",channel_name,re.IGNORECASE) and not re.search("DISCOVERY KIDS",channel_name,re.IGNORECASE) and not re.search("DISNEY",channel_name,re.IGNORECASE) and not re.search("GLOOB",channel_name,re.IGNORECASE) and not re.search("NAT GEO KIDS",channel_name,re.IGNORECASE) and not re.search("NICKELODEON",channel_name,re.IGNORECASE) and not re.search("NICK JR",channel_name,re.IGNORECASE) and not re.search("PLAYKIDS",channel_name,re.IGNORECASE) and not re.search("TOONCAST",channel_name,re.IGNORECASE) and not re.search("ZOOMOO",channel_name,re.IGNORECASE):
                        pass
                    #Documentario
                    elif int(filtrar) == 5  and re.search("Kids",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 5 and not re.search("Discovery",channel_name,re.IGNORECASE) and not re.search("H2 HD",channel_name,re.IGNORECASE) and not re.search("H2 SD",channel_name,re.IGNORECASE) and not re.search("H2 FHD",channel_name,re.IGNORECASE) and not re.search("History",channel_name,re.IGNORECASE) and not re.search("Nat Geo Wild",channel_name,re.IGNORECASE) and not re.search("National Geographic",channel_name,re.IGNORECASE):
                        pass
                    #Abertos
                    elif int(filtrar) == 6 and re.search("Brasileirinhas",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 6 and re.search("News",channel_name,re.IGNORECASE) or int(filtrar) == 6 and re.search("Sat",channel_name,re.IGNORECASE) or int(filtrar) == 6 and re.search("FM",channel_name,re.IGNORECASE):
                        pass
                    elif int(filtrar) == 6 and not re.search("Globo",channel_name,re.IGNORECASE) and not re.search("RECORD",channel_name,re.IGNORECASE) and not re.search("RedeTV",channel_name,re.IGNORECASE) and not re.search("Rede Vida",channel_name,re.IGNORECASE) and not re.search("SBT",channel_name,re.IGNORECASE) and not re.search("TV Brasil",channel_name,re.IGNORECASE) and not re.search("TV Cultura",channel_name,re.IGNORECASE) and not re.search("TV Diario",channel_name,re.IGNORECASE) and not re.search("BAND",channel_name,re.IGNORECASE):
                        pass
    
