   아마존 닷컴 사이트에서 best seller 상품의 정보를 추출하는 크롤러를 만드는데 제품의 이미지까지 추출하여 엑셀 파일로 저장하도록 크롤러를 만드세요
   
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

    query_txt = ("아마존 닷컴")
    query_url = "https://www.amazon.com/bestsellers?id=NSGoogle"
    print("= " * 80)
    print("아마존 닷컴의 분야별 best seller 상품 정보 추출하기")
    print("= " * 80)

    sec = input('''
          1.Amazon Devices & Accessories   2.Amazon launchpad            3.Applicanes
          4.App & Games                    5.Arts, Crafts & Sewing       6.Audible Books & Originals
          7. Automotive                    8.baby                        9.Beauty & Personal Care
          10. Books                        11.CDs & Vinyl                12.Camera & photo
          13.Cell Phones & Accessories     14.Clothing, shoes & Jewelry  15.Collectible Currencies
          16.Computer & Accessories        17.Digital Music              18.Electronics
          19. Entertainment Collectibies   20.Gift Cards                 21.Grocery & Gourmet Food
          22.Handmade Products             23.Health & Household         24.Home & Kitchen
          25.Industrial & Scientific       26.Kindle Store               27.Kitchen & Dining
          28.Magazine Subcriptions         29.Movies % TV                30.Musical Instruments
          31.Office Products               32.Patio, Lawn & Garden       33.Pet Supplies
          34.Prime Pantry                  35.Smart Home                 36.Software
          37.Sports & Outdoors             38.Sports Collectibies        39.Tools & Home Improvement
          40.Toys & Games                  41. Yideo Games ''')

    cnt = int(input("크롤링 할 건수는 몇건입니까?"))
    f_dir = input("결과 파일을 저장할 폴더명만 쓰세요")
    if sec == '1' :
        sec_name = 'Amazon Devices & Accessories'
    elif sec == '2' :
        sec_name = 'Amazon launchpad '
    elif sec == '3' :
        sec_name = '.Applicanes'
    elif sec == '4' :
        sec_name = 'App & Games'
    elif sec == '5' :
        sec_name = 'Arts, Crafts & Sewing'
    elif sec == '6' :
        sec_name = 'Audible Books & Originals'
    elif sec == '7' :
        sec_name = ' Automotive '
    elif sec == '8' :
        sec_name = 'baby'
    elif sec == '9' :
        sec_name = 'Beauty & Personal Care'
    elif sec == '10' :
        sec_name = ' Books      '
    elif sec == '11' :
        sec_name = 'CDs & Vinyl  '
    elif sec == '12' :
        sec_name = 'Camera & photo'
    elif sec == '13' :
        sec_name = 'Cell Phones & Accessories '
    elif sec == '14' :
        sec_name = '.Clothing, shoes & Jewelry'
    elif sec == '15' :
        sec_name = 'Collectible Currencies'
    elif sec == '16' :
        sec_name = 'Computer & Accessories  '
    elif sec == '17' :
        sec_name = 'Digital Music '
    elif sec == '18' :
        sec_name = 'Electronics'
    elif sec == '19' :
        sec_name = 'Entertainment Collectibies'
    elif sec == '20' :
        sec_name = 'Gift Cards      '
    elif sec =='21' :
        sec_name = 'Grocery & Gourmet Food'
    elif sec == '22' :
        sec_name = 'Handmade Products    '
    elif sec == '23' :
        sec_name = 'Health & Household   '
    elif sec == '24' :
        sec_name = 'Home & Kitchen'
    elif sec == '25' :
        sec_name = 'Industrial & Scientific '
    elif sec == '26' :
        sec_name = 'Kindle Store      '
    elif sec == '27' :
        sec_name = 'Kitchen & Dining'
    elif sec == '28' :
        sec_name = 'Magazine Subcriptions'
    elif sec == '29' :
        sec_name = 'Movies % TV   '
    elif sec == '30' :
        sec_name = 'Musical Instruments'
    elif sec == '31' :
        sec_name = 'Office Products'
    elif sec == '32' :
        sec_name = 'Patio, Lawn & Garden'
    elif sec == '33' :
        sec_name = 'Pet Supplies'
    elif sec == '34' :
        sec_name = 'Prime Pantry '
    elif sec == '35' :
        sec_name = 'Smart Home   '
    elif sec == '36' :
        sec_name = '.Software'
    elif sec == '37' :
        sec_name = 'Sports & Outdoors  '
    elif sec == '38' :
        sec_name = 'Sports Collectibies'
    elif sec == '39' :
        sec_name = 'Tools & Home Improvement'
    elif sec == '40' :
        sec_name = 'Toys & Games'
    elif sec == '41' :
        sec_name = 'Yideo Games'
    if cnt > 30:
        print("잠시만 기다려 주세요")
    else:
        print("요청하신 데이터를 수집하고 있으니 잠시만 기다려 주세요")

    n = time.localtime()
    s = "%04d-%02d-%02d-%02d-%02d-%02d" % (n.tm_year , n.tm_mon, n.tm_mday , n.tm_hour, n.tm_min, n.tm_sec )

    os.makedirs(f_dir + s + '-'+ query_txt +'-'+ sec_name)
    os.chdir(f_dir + s + '-'+ query_txt +'-'+ sec_name)


    ff_dir = f_dir + s + '-'+ query_txt +'-'+ sec_name
    ff_name = f_dir + s + '-'+ query_txt +'-'+ sec_name + '\\' + s + '-' + query_txt + '-' + sec_name + '.txt'
    fx_name = f_dir + s + '-'+ query_txt +'-'+ sec_name + '\\' + s + '-' + query_txt + '-' + sec_name + '.xlsx'
    fxx_name = f_dir + s + '-'+ query_txt +'-'+ sec_name + '\\' + s + query_txt + '.xlsx'


    s_time = time.time()
    path = "C:/bigdata_test/chromedriver.exe"
    driver = webdriver.Chrome(path)
    driver.get(qurey_url)
    time.sleep(5)
    driver.find_element_by_xpath("//*[@id=\"zg_browseRoot\"]/ul/li["+sec+"]/a").click()
    time.sleep(1)

    def scroll_down(driver):
        driver.execute_script("window.scrollBy(0,9300);")
        time.sleep(1)

    scroll_down(driver)

    bmp_map = dict.fromkeys(range(0x10000, sys.maxunicode +1 ), 0xfffd)

    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')

    file_no = 0
    reple_result= soup.select('#zg-center-div > #zg-ordered-list')
    slist = reple_result[0].find_all('li')

    if cnt < 51 :
        ranking2 = []
        title3 = []
        price2 = []
        score2 = []
        sat_count2 = []
        store2= []
        image2 =[]
        count =0


        for li in slist:
            f = open(ff_name, 'a' , encoding = 'UTF-8')
            f.write("----------------------------------------" + "\n")
            print("-"*70)
            try:
                ranking = li.find('span', class_='zg-badge-text').get_text().replace("#",'')
            except AttributeError:
                ranking = ''
                print(ranking.replace("#",""))
            else:
                print("1. 판매순위", ranking)
                f.write('1.판매순위' + ranking+ "\n")

            try:
                title1 = li.find('div' , class_ = 'p13n-sc-truncated').get_text().replace("\n","")
            except AttributeError:
                title1 = ''
                print(title1.replace("#", ""))                                                                          
                f.write('2.제품소개:' +title1+ "\n")                                                                          
            else:
                title2 = title1.translate(bmp_map).replace("\n","")
                print('2.제품소개:' +title2)
                count +=1
                f.write("2.제품소개" + title2 + "\n")

                img1 = li.find('img')['src']
                x = urllib.request.urlretrieve(img1, str(file_no)+'.jpg')
                image1 = Image(ff_dir + '/' + str(file_no) + ".jpg")
                file_no = file_no +1

                try:
                    price = li.find('span', 'p13n-sc-price').get_text().replace("\n", "")
                except AttributeError:
                    price = ''
                print("3.가격:", price.replace("\n",""))                                                                             
                f.write("3.가격" + price + "\n")
                try:
                    sat_count = li.find('a', 'a-size-small a-link-normal').get_text().replace(",","")
                except (IndexError , AttributeError):                                                                      
                    sat_count ='0'                                                                      
                    print('4.상품평 수 :' ,sat_count)                                                                      
                    f.write('4.상품평 수 :' + sat_count + '\n')
                else:
                    print('4. 상품평수:', sat_count)
                    f.write("4. 상품평 수"  + sat_count + "\n") 
                try:
                    score = li.find('span', 'a-icon-alt').get_text()                                                                      
                except AttributeError:
                    score = ''
                print('5.평점:',score) 
                f.write('5.평점:' + score + '\n')
                print("-"*70)
                f.close()
                time.sleep(2)


                ranking2.append(ranking)
                title3.append("\n" + title2.replace("\n",""))
                price2.append(price.replace("\n",""))
                try:
                    sat_count2.append(sat_count)
                except AttributeError:
                    sat_count2.append(0)

                image2.append(image1)
                score2.append(score)

                if count == cnt:
                    break
    elif cnt >= 51:
        ranking2 = []
        title3 = []
        price2 = []
        score2 = []
        sat_count2 = []
        store2 = []

        count = 0

        for li in slist :
            f = open(ff_name, 'a', encoding = 'UTF-8')
            f.write("-----------------------------------------------------------------------")
            print("-"*70)
            try:
                ranking = li.find('span', class_='zg-badge-text').get_text().replace("#","")
            except AttributeError:
                ranking = ''
                print(ranking.replace("#",""))
            else:
                print("1.판매순위" + ranking + "\n")
                f.write("1. 판매순위" + ranking + " \n")

            try:
                title1 =li.find('div' , class_='p13n-sc-truncated').get_text().replace("\n","")
            except AttributeError:
                title1 = ''
                print(title1.replace('#',''))
                f.write("2. 제품소개:" + title1 + "\n")
            else:
                title2 = title1.translate(bmp_map).replace("\n" , "")
                print('2. 제품소개:' + title2)
                count += 1
                f.write("2. 제품소개" + title2 + "\n")
                img1 = li.find('img')['src']
                x = urllib.request.urlrtrieve(img1, str(file_no) + '.jpg')
                image1 = Image(ff_dir + '/' + str(file_no) + ".jpg")                                                                        
                file_no = file_no + 1

            try:
                price = li.find('span', 'p13n-sc-price').get_text().replace("\n", "")
            except AttributeError:
                price = ''
            print('3. 가격:' , price.replace("\n", "")) 
            f.write('3.가격' + price + "\n")

            try:
                sat_count = li.find('a', 'a-size-small a-link-normal').get_text().replace(',',"")
            except (IndexError, AttributeError):
                sat_count = '0'
                print("4.상품평 수 :", sat_count)                           
                f.write('4. 상품평 수' + sat_count + "\n")
            else:
                print("4.상품평 수 :", sat_count)                           
                f.write('4. 상품평 수' + sat_count + "\n")                           
            try:
                score = li.find('span' , 'a-icon-alt').get_text()
            except AttributeError:
                score = ''
            print("5. 평점:" + score)                               
            f.write("5. 평점:" + score + "\n")
            print("-"*70)                               
            f.close()                                                                            
            time.sleep(0,5)                               
            ranking2.append(ranking)                          
            title3.append(title2.replace("\n", ""))                          
            price2.appned(price.replace("\n" , ""))                          
            try:                          
                sat_count2.appned(sat_count)                      
            except IndexError:                          
                sat_count2.append(0)                      
            score2.append(score)

        driver.find_element_by_xpath("""//*[@id="zg-center-div"]/div[2]/div/ul/li[3]/a""").click()                              
        print("\n")                              
        print("요청하신 데이터의 수량이 많아 다음 페이지의 데이터를 추출 중이니 잠시만 기다려 주세요")                              
        print("\n")

        html = driver.page_source
        soup = BeautifulSoup(html, 'html.parser')                              

        reple_result = soup.select('#zg-center-div > #zg-ordered-list')                              
        slist = reple_result[0].fond_all('li')

        for li in slist:                              
            f=open(ff_name , 'a' , encoding = 'UTF-8')                         
            f.wrie("-----------------------------------------------" + "\n")                         
            print("-"*70)                         
            try :                         
                ranking = li.find('span' , class_ = 'zg-badge-text').get_text().replace("#" , "")
            except AttributeError:                         
                ranking = ' '                   
                print(ranking.replace('#',''))                   
            else:                         
                print("1. 판매순위", ranking)                    
                f.write('1. 판매순위' + ranking + '\n')                    

            try:                         
                title1 = li.find('div' , class_ = 'p13n-sc-truncated').get_text().replace("\n","")                     

            except AttributeError:                         
                title1= ''                     
                print(title1.replace("#",""))                     
                f.write("2. 제품소개:" + title1 + "\n")                     
            else:                         
                title2 = title1.translate(bmp_map).repalce("\n","")                    
                print('2. 제품소개:' + title2)                     
                count +=1                     
                f.write('2.제품소개' + title2 +  "\n")                     

                img1 = li.find('img')["src"]                     
                x = urllib.request.urlretrieve(img1, str(file_no) + '.jpg')                     
                image1 = Image(ff_dir + '/' + str(file_no) + ".jpg")                     
                file_no = file_no +1                     


            try :                         
                price = li.find('span' , 'p13n-sc-price').get_text().repalce("\n","")                     
            except AttributeError:                         
                price = ''                     
            print('3. 가격:' , price.replace("\n" , ""))                         
            f.write("3.가격" + price + "\n")     

            try:                         
                sat_count = li.find('a' , 'a-size-small a-link-normal').get_text().replace(",","")                     

            except (IndexError, AttributeError) :                         
                sat_count =0                     
                print("4.상품평 수 :" , sat_count)                     
                f.write('4. 상품평 수' + sat_count + "\n")
            else:                        
                print("4.상품평 수:" , sat_count)                   
                f.write("4.상품평 수" + sat_count + "\n")                   

            try:                       
                score = li.find('span' , 'a-icon-alt').get_text()                   
            except AttributeError:                       
                score = ''                   
            print("5. 평점" , score)                       
            f.write('5. 평점:' + score + "\n")                       
            print("5. 평점" + score + "\n")                       
            f.close()                       
            time.sleep(0.5)   

            ranking2.append(ranking)                       
            title3.append(img1 + "\n" + title2.replace("\n", "") )                       
            price2.appned(priccnte.repalce("\n",""))                       
            try:                       
                sat_count2.append(sat_count)                   
            except IndexError:                       
                sat_count2.append(0)                   
            score2.append(score)                       
            image2.appned(image1)                       
            if count == cnt:                       
                break;                
    else:
          print("검색 건수는 1건 ~ 최대 100건 까지 입니다")    

    amazon_best_seller = pd.DataFrame()
    amazon_best_seller['판매순위'] = ranking2
    amazon_best_seller['제품소개'] = pd.Series(title3)
    amazon_best_seller['판매가격'] = pd.Series(price2)
    amazon_best_seller['상품평 갯수'] = pd.Series(sat_count2)
    amazon_best_seller['상품평점'] = pd.Series(score2)
    amazon_best_seller.to_excel(fx_name, index = False)

    print("\n")

    excel = openpyxl.load_workbook(fx_name)                                          
    count =2; 

    for t in image2:
        ImageLoader = excel.active
        ImageLoader.add_image(t, 'B%s' %(count))                                     
        count +=1                                      

    excel.save(fxx_name)                                          



                                    
