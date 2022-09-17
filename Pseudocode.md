INITIALIZE dailySpamEmailsCount to 0
INITIALIZE dailySalesEmailsCount to 0
INITIALIZE dailyRecruitmentEmailsCount to 0
INITIALIZE dailyReceptionEmailsCount to 0
INITIALIZE dailyTotalSortedEmails to 0

FUN forwardEmail (emailToBeForwarded, departmentEmail)
    FORWARD email to departmentEmail
END FUN

FUN deleteEmail(emailToBeDeleted) 
    DELETE emailToBeDeleted
END FUN

FUN add(hourlyEmailCount,dailyEmailCount)
    UPDATE dailyEmailCount by ADDING hourlyEmailCount to its previous value
END FUN

FUN printReport(
    recruitmentEmailsCount,
    spamEmailsCount,
    salesEmailsCount,
    receptionEmailsCount,
    totalSortedEmails)
        We have sorted totalSortedEmails mails:
            recruitmentEmailsCount to recruitment
            spamEmailsCount to spam
            salesEmailsCount to sales
            receptionEmailsCount to reception
END FUN

Every hour,IF time is not 5pm AND number of unsorted emails is greater than or equal to 100 THEN
    INITIALIZe hourlyRecruitmentEmailsCount to 0;
    INITIALIZe hourlySpamEmailsCount to 0;
    INITIALIZe hourlySalesEmailsCount to 0;
    INITIALIZe hourlyReceptionEmailsCount to 0;
    INITIALIZe hourlyTotalSortedEmails to 0;

    FOR each unsorted email
        IF email CONTAINS the word "CV" THEN
            CALL forwardEmail(currentEmail,“recruitment@parkshark.com")
            CALL deleteEmail(currentEmail)
            INCREMENT hourlyRecruitmentEmailsCount by 1

        ELSE IF email CONTAINS the word "Promo" OR "advertising" THEN
            CALL forwardEmail(currentEmail,"spam@parkshark.com")
            CALL deleteEmail(currentEmail)
            INCREMENT hourlySpamEmailsCount by 1

        ELSE IF email CONTAINS the word "proposal" THEN
            CALL forwardEmail(currentEmail,"sales@parkshark.com")
            CALL deleteEmail(currentEmail)
            INCREMENT hourlySalesEmailsCount by 1
        
        ELSE 
            CALL forwardEmail(currentEmail,"reception@parkshark.com")
            CALL deleteEmail(currentEmail)
            INCREMENT hourlyReceptionEmailsCount by 1
        END IF
        INCREMENT hourlyTotalSortedEmailsCount by 1
    END FOR

    CALL add(hourlyRecruitmentEmailsCount,dailyRecruitmentEmailsCount)
    CALL add(hourlySpamEmailsCount,dailySpamEmailsCount)
    CALL add(hourlySalesEmailsCount,dailySalesEmailsCount)
    CALL add(hourlyReceptionEmailsCount,dailyReceptionEmailsCount)
    CALL add(hourlyTotalSortedEmails,dailyTotalSortedEmails)

    CALL printReport(
        hourlyRecruitmentEmailsCount,
        hourlySpamEmailsCount,
        hourlySalesEmailsCount,
        hourlyReceptionEmailsCount,
        hourlyTotalSortedEmails)

ELSE IF time is 5pm
    PRINT report for the entire day
    CALL printReport(
        dailyRecruitmentEmailsCount,
        dailySpamEmailsCount,
        dailySalesEmailsCount,
        dailyReceptionEmailsCount,
        dailyTotalSortedEmails)
ENDIF

