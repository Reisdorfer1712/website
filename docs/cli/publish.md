import flet as ft

def menu(paginaReisdorfer):
    texto = ft.Text("ReisdörferZap", size=20)

    chat=ft.Column()

    nome_usuario=ft.TextField(label="Escreva seu nome")#label é para colocar a sugestão na tela

    def enviar_mensagem_tunel(mensagem):
        tipo = mensagem["tipo"]
        if tipo == "mensagem":
            texto_mensagem = mensagem["texto"]
            usuario_mensagem = mensagem["usuario"]
            chat.controls.append(ft.Text(f"{usuario_mensagem}: {texto_mensagem}", size=18))#append = a adicionar
        else:
            usuario_mensagem = mensagem["usuario"]
            chat.controls.append(ft.Text(f"{usuario_mensagem} entrou no Chat", italic=True, size=14, color=ft.colors.ORANGE_500))
        paginaReisdorfer.update()


    paginaReisdorfer.pubsub.subscribe(enviar_mensagem_tunel)

    def enviar_mensagem(evento):
        paginaReisdorfer.pubsub.send_all({"texto": campo_mensagem.value, "usuario": nome_usuario.value, "tipo": "mensagem"})#send_all para todo mundo
        campo_mensagem.value=""#value é o valor que estara na variavel
        paginaReisdorfer.update()


    campo_mensagem=ft.TextField(label="Digite sua mensagem", on_submit=enviar_mensagem)

    botao_enviar=ft.ElevatedButton("Enviar", on_click= enviar_mensagem)

    def entrar_popup(evento):
        paginaReisdorfer.pubsub.send_all({"usuario": nome_usuario.value, "tipo": "entrada"})
        paginaReisdorfer.add(chat)
        popup.open = False
        paginaReisdorfer.remove(botao_inicio)
        paginaReisdorfer.remove(texto)
        paginaReisdorfer.add(ft.Row(
            [campo_mensagem, botao_enviar]
        ))
        paginaReisdorfer.update()


    popup = ft.AlertDialog(
        open=False,
        modal=True,
        title=ft.Text("Bem Vindo ao ReisdörferZap"),
        content= nome_usuario,
        actions=[ft.ElevatedButton("Entrar", on_click=entrar_popup)],
        )#modal é o nome usado pela internet

    def entrar_chat(evento):
        paginaReisdorfer.dialog = popup #dialog alertas ou popups que aparecem na página
        popup.open = True
        paginaReisdorfer.update()


    botao_inicio = ft.ElevatedButton("Iniciar Chat", on_click=entrar_chat)

    paginaReisdorfer.add(texto)
    paginaReisdorfer.add(botao_inicio)


ft.app(target=menu, view=ft.WEB_BROWSER)#o view=ft.WEB_BROWSER é para mostrar como você quer que apareça um app por padrão ou um site como aparece 
