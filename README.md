

    name=StringField("İsim Soyisim",validators=[validators.Length(min=4,max=25)])
    username=StringField("Kullanıcı Adı",validators=[validators.Length(min=5,max=35)])
    email=StringField("Email Adresi",validators=[validators.Email(message="Lütfen Geçerli bir Email adresi girin.... ")])
    password=PasswordField("Parola:",validators=[
        validators.DataRequired(message="Lütfen bir parola belirleyin."),
        validators.EqualTo(fieldname="confirm",message="Parolanız Uyuşmuyor...")
    ])
    confirm=PasswordField("Parola Doğrula")


    if request.method=="POST":
        username=form.username.data
        password_entered=form.password.data
        cursor=mysql.connection.cursor()
        sorgu="Select *From users where username=%s"
        result=cursor.execute(sorgu,(username,))

        if result>0:
            data=cursor.fetchone()
            real_password=data["password"]
            if sha256_crypt.verify(password_entered,real_password):
              flash("Başarıyla giriş yaptınız..","success")

              session["logged_in"]=True
              session["username"]=username

              return redirect(url_for("index"))
            else:
                flash("Parolanızı yanlış girdiniz...","danger")
                return redirect(url_for("login"))
        else:
            flash("Böyle bir kullanıcı bulunmuyor...","danger")
            return redirect(url_for("login"))
    return render_template("login.html",form=form)

#Detay Sayfası


    if result>0:
       article=cursor.fetchone()
       return render_template("article.html",article=article)
    else:
        return render_template("article.html")


        cursor=mysql.connection.cursor()
        sorgu="Insert into articles(title,author,content) VALUES(%s,%s,%s)"
        cursor.execute(sorgu,(title,session["username"],content))
        mysql.connection.commit() 
        cursor.close()
        flash("Makale Başarıyla Eklendi...","success")
        return redirect(url_for("dashboard"))
    return render_template("addarticle.html",form=form)


    if result>0:
        sorgu2="Delete from articles where id=%s"
        cursor.execute(sorgu2,(id,))
        mysql.connection.commit()
        return redirect(url_for("dashboard"))
    else:
        flash("Böyle bir makale yok veya bu işlemi yapmaya yetkiniz yok...","danger")
        return redirect(url_for("index"))


            form.title.data=article["title"]
            form.content.data=article["content"]
            return render_template("update.html",form=form)
    else:
        #POST REQUEST
        form=ArticleForm(request.form)
        newTitle=form.title.data
        newContent=form.content.data
        sorgu2="Update articles Set title =%s,content=%s where id=%s"
        cursor=mysql.connection.cursor()
        cursor.execute(sorgu2,(newTitle,newContent,id))
        mysql.connection.commit()

        flash("Makale başarıyla güncellendi.","success")
        return redirect(url_for("dashboard"))



    app.run(debug=True)
