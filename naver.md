    from bs4 import BeautifulSoup
    from selenium import webdriver
    import time
    import sys
    import re
    import math
    import numpy
    import pandas as pd
    import xlwt
    import random
    import os
    from selenium.webdriver.support.ui import Select


    txt = "리뷰 데이터 수집하기"

    print("= " * 80)
    print("네이버 영화 리뷰 및 평점 수집하기")
    print("= " * 80)

    title = input("1. 크롤링 할 영화의 제목을 입력하세요")
    cnt = int(input("2. 크롤링 할 건수는 몇건입니까?"))

    page_cnt = math.ceil(cnt/10)

    f_dir = input("3. 결과 파일을 저장할 폴더명만 쓰세요")
    n = time.localtime()
    s = "%04d-%02d-%02d-%02d-%02d-%02d" % (n.tm_year , n.tm_mon, n.tm_mday , n.tm_hour, n.tm_min, n.tm_sec )

    os.makedirs(f_dir + s + '-'+ txt)
    os.chdir(f_dir + s + '-' + txt)

    f_name =  f_dir+s + '-'+txt +'\\'+s+'-'+ txt+'.txt'
    fc_name =  f_dir+s + ' -'+txt +'\\'+s+'-'+ txt+'.csv'
    name = f_dir+s + ' -'+txt +'\\'+s+'-'+ txt
    fx_name =   f_dir+s + '-'+txt +'\\'+s+'-'+ txt+'.xls'

    s_time = time.time()
    path = "C:/bigdata_test/chromedriver.exe"
    driver = webdriver.Chrome(path)
    driver.get("https://movie.naver.com")
    time.sleep(random.randrange(2,5))

    driver.find_element_by_id("ipt_tx_srch").send_keys(title)
    driver.find_element_by_class_name("btn_srch").click()
    time.sleep(3)

    driver.find_element_by_xpath("//*[@id=\"old_content\"]/ul[2]/li[1]/dl/dt/a").click()
    time.sleep(2)
    driver.find_element_by_xpath("//*[@id=\"movieEndTabMenu\"]/li[5]/a/em").click()



    print("크롤링할 총 페이지 번호", page_cnt)
    print("="*80)

    score2= []
    contents2= []
    name2 = []
    day2 = []
    good2 = []
    bad2 =[]
    no =1

    html = driver.page_source
    soup = BeautifulSoup(html, "html.parser")

    m = soup.find("iframe",{"id" : "pointAfterListIframe"})
    driver.get("http://movie.naver.com" + m['src'])
    for x in range(1, page_cnt+1):
        print("%s 페이지 내용 수집 시작합니다 ==========="  %x)
        html = driver.page_source
        soup = BeautifulSoup(html, 'html.parser')

        score = soup.find('div', "score_result").find('ul')
        result = score.find_all('li')
        count =0

        for i in result:
            print(i.get_text())
            f = open(f_name , 'a' , encoding = "UTF-8")
            if no > cnt :
                break
            score1 = i.find("div" , "star_score").get_text().replace("", "").replace("\n", "")
            content1 = i.find("div" , "score_reple").find("p").get_text().replace("", "").replace("\n", "").replace("\t", "")
            review = i.find_all("em")
            name1 = review[0].get_text().replace(" " , "").replace("\n", "")
            day1 = review[1].get_text().replace("" , "").replace("\n", "")
            good1 = i.find("a", "_sympathyButton").get_text().replace("공감" , "").replace("" , "").replace("\n" , "")
            bad1 = i.find("a", "_notSympathyButton").get_text().replace("비공감", "").replace("" , "").replace("\n" , "")

            f.write(str(no) + "번째 리뷰 수집합니다")
            f.write("1. 별점" +  str(score1) + "\n")
            f.write("2. 리뷰내용" +str(content1) + "\n")
            f.write("3. 작성자"+str(name1) + "\n" )
            f.write("4. 작성일자"+str(day1) + "\n")
            f.write("5. 공감"+str(good1) + "\n")
            f.write("6. 비공감"+str(bad1) + "\n")
            f.close()

            score2.append(score1)
            contents2.append(content1)
            name2.append(name1)
            day2.append(day1)
            good2.append(good1)
            bad2.append(bad1)
            time.sleep(1)
            count += 1
            no += 1

        if  x> page_cnt + 1:
            break;
        driver.find_element_by_link_text("다음")
        x += 1

    korea = pd.DataFrame()
    korea['별점'] = score2
    korea['리뷰내용'] = contents2
    korea['작성자']=name2
    korea['작성일자']=day2
    korea['공감']=good2
    korea['비공감']=bad2


    korea.to_excel(fx_name, index =False)

    def createFolder(directory):
        try:
            if not os.path.exists(directory):
                os.makedirs(directory)
        except OSError:
            print ('Error: Creating directory. ' +  directory)

    createFolder(name)



    korea.to_csv(fc_name, encoding = "utf-8-sig", index =False)
    e_time = time.time()
    t_time = e_time = s_time

    print("\n")
    print("=" * 80)
    print("총 소요시간은 %s 초 입니다" %round(t_time,1))

    print("파일저장완료:txt 파일명: %s" %f_name)
    print("파일저장완료:csv 파일명: %s" %fc_name)
    print("파일 저장 완료 : xls 파일명 : %s" %fx_name)
    print("=" * 80)
    driver.close()


