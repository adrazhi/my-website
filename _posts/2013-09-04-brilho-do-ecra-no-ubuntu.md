---
layout: post
title: Resolver problemas com o brilho do ecrã no Ubuntu
lang: pt
---


Após ter instalado a última versão do sistema operativo Ubuntu (13.04) num computador portátil <strong>Samsung NP-RV510</strong> deparei-me com o facto das teclas destinadas à regulação do brilho no ecrã estarem a funcionar incorrectamente.

Pensei em ir a <strong>Definições do Sistema -> Brilho & Bloqueio</strong> mas o ecrã continuou sem alterações significativas.

Sendo assim, a solução passou por efectuar as seguintes instruções:

<strong>1- Executar o comando no terminal (Ctrl+Alt+T):</strong>
{% highlight text %}
sudo nano /etc/default/grub
{% endhighlight %}

<strong>2- Introduzir a linha no ficheiro grub e guardar as alterações:</strong>
{% highlight text %}
GRUB_CMDLINE_LINUX="acpi_backlight=vendor"
{% endhighlight %}

<strong>3- Por fim, executar o comando:</strong>
{% highlight text %}
sudo update-grub && sudo reboot
{% endhighlight %}
