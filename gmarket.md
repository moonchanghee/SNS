지마켓 사이트에서 best seller 상품의 정보를 수집하는 크롤러를 작성하세요
    
    from bs4 import BeautifulSoup
    from selenium import webdriver
    import openpyxl
    from openpyxl.drawing.image import Image
    import urllib.request
    import urllib
    import time
    import sys
    import re
    import math
    import numpy
    import pandas as pd
    import xlwt
    import random
    import os

    query_txt = ("G마켓 베스트셀러")
    query_url = "http://corners.gmarket.co.kr/Bestsellers"

    cnt = int(input("크롤링 할 건수는 몇건입니까?"))
    f_dir = input("결과 파일을 저장할 폴더명만 쓰세요")


    n = time.localtime()
    s = "%04d-%02d-%02d-%02d-%02d-%02d" % (n.tm_year , n.tm_mon, n.tm_mday , n.tm_hour, n.tm_min, n.tm_sec )

    os.makedirs(f_dir + s + '-'+ query_txt)
    os.chdir(f_dir + s + '-'+ query_txt)

    ff_dir = f_dir + s + '-'+ query_txt
    ff_name = f_dir + s + '-'+ query_txt +'\\' + s + query_txt + '-' + '.txt'
    fx_name = f_dir + s + '-'+ query_txt +'\\' + s + query_txt + '-' + '.xlsx'
    fxx_name = f_dir + s + '-'+ query_txt +'\\' + s + query_txt + '-' +'.xlsx'

    s_time = time.time()
    path = "C:/bigdata_test/chromedriver.exe"
    driver = webdriver.Chrome(path)
    driver.get(query_url)
    time.sleep(5)

    def scroll_down(driver) :
        driver.execute_script("window.scrollBy(0,9300);")
        time.sleep(3)

    scroll_down(driver)

    bmp_map = dict.fromkeys(range(0x10000, sys.maxunicode +1 ), 0xfffd)
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')

    bestSeller = soup.find_all('div' , 'best-list')[1].find('ul')
    best_list = bestSeller.find_all('li')

    ranking2 = []
    title2 = []
    A_price2 = []
    B_price2 = []
    sale2 = []
    image2=[]
    count =0;
    file_no = 0;

    for I in best_list:
        f=open(ff_name, 'a', encoding='UTF-8')
        f.write("-------------------------------------" + "\n")
        print("-"*80)
        count = count +1

        ranking = I.find("p").get_text().replace("\n" ,"").replace("","")
        print("1 판매순위:" ,ranking)
        f.write("1. 판매순위" + ranking + "\n")

        img1 = I.find('img')['src']                     
        x = urllib.request.urlretrieve(img1, str(file_no) + '.jpg')                     
        image1 = Image(ff_dir + '/' + str(file_no) + ".jpg")                     
        file_no = file_no +1      

        title = I.find('a' , 'itemname').get_text()
        title1 = title.translate(bmp_map).replace("\n","")
        print("2.제품소개 : " + title1)
        f.write("2.제품소개 : " + title1 + "\n")

        try:
            B_price = I.find('div', 's-price').get_text().replace("\n","").replace("","")
            print("3.원래가격:",B_price)
            f.write("3.원래가격 :" + B_price + "\n")

        except:
            B_price = I.find('div', 's-price').get_text().replace("\n","").replace("","")
            print("3.원래가격" , B_price)
            f.write("3.원래가격 :" + B_price + "\n")
            A_price = B_price
            sale = "0%"
            print("4. 판매가격 : ", A_price)
            f.write("4.판매가격" + A_price + "\n")
            print("5.할인율:"+ sale)
            f.write("5.할인율"+ sale + "\n")


        else:
                A_price = I.find("div" , "s-price").find("strong").get_text().replace("\n","").replace("","")
                print("4.판매가격:" , A_price)
                f.write("4.판개가격" + A_price + "\n")
                sale = I.find('em').get_text().replace("\n" , "").replace("","")
                print("5.할인율:"+ sale)
                f.write("5.할인율" + sale + "\n")

        f.close()

        ranking2.append(ranking)
        title2.append(title1)
        A_price2.append(A_price)
        B_price2.append(B_price)
        sale2.append(sale)
        image2.append(image1)

        if count == cnt:
             break                                                   




    gmarket_best_seller = pd.DataFrame()
    gmarket_best_seller['판매순위'] = ranking2
    gmarket_best_seller['제품소개'] = pd.Series(title2)
    gmarket_best_seller['원래가격'] = pd.Series(B_price2)
    gmarket_best_seller['판매가격'] = pd.Series(A_price2)                                               
    gmarket_best_seller['할인율'] = pd.Series(sale2)                                                


    gmarket_best_seller.to_excel(fx_name , index=True)

    excel = openpyxl.load_workbook(fx_name)
    count =2
    for t in image2:
        ImageLoader = excel.active                                               
        ImageLoader.add_image(t, 'C%s'%(count))
        count += 1
    excel.save(fxx_name)

    ##C:\Users\py_temp\data\\
