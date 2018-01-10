<template>
  <div
    class="intl-phone-input allow-dropdown"
    v-on:mouseleave="hideList"
  >
    <div
      class="flag-container"
      v-on:click.stop="toggeList"
    >
      <div class="selected-flag">
        <div v-bind:class="`iti-flag ${countryCode}`"></div>
        <div v-bind:class="`iti-arrow${isVisiblePanel ? ' up' : ''}`"></div>
      </div>
      <ul class="country-list" v-if="isVisiblePanel">
        <li
          v-for="item in itemsData.frontItems"
          v-bind:class="`country${item.code === countryCode ? ' highlight' : ''}`"
          v-on:click.stop="setCode(item.code)"
        >
          <div class="flag-box">
            <div v-bind:class="`iti-flag ${item.code}`"></div>
          </div>
          <span class="country-name">{{ item.name }}</span>
          <span class="dial-code">+{{ item.dialCode }}</span>
        </li>
        <li class="divider" v-if="itemsData.needSeparator"></li>
        <li
          v-for="item in itemsData.otherItems"
          v-bind:class="`country${item.code === countryCode ? ' highlight' : ''}`"
          v-on:click.stop="setCode(item.code)"
        >
          <div class="flag-box">
            <div v-bind:class="`iti-flag ${item.code}`"></div>
          </div>
          <span class="country-name">{{ item.name }}</span>
          <span class="dial-code">+{{ item.dialCode }}</span>
        </li>
      </ul>
    </div>
    <input type="text"
      autocomplete="off"
      v-bind:name="name"
      v-on:input="handleChangePhoneNumber"
      v-bind:value="phone.number"
      @keyup="changeCountry"
      v-bind:placeholder="intlData.example"
    />
  </div>
</template>

<script>
  /* eslint-disable no-unused-vars */

  const has = Object.hasOwnProperty;
  const isObject = maybeObj => Object.prototype.toString.call(maybeObj) === '[object Object]';
  const isArray = maybeArray => Object.prototype.toString.call(maybeArray) === '[object Array]';
  const isString = maybeString => Object.prototype.toString.call(maybeString) === '[object String]';
  const isFunction = maybeFunc => Object.prototype.toString.call(maybeFunc) === '[object Function]';

  /* eslint-disable object-curly-newline */

    const phonesData = {
        af: { code: 'af', name: 'Afghanistan (‫افغانستان‬‎)', dialCode: 93, example: '070 123 4567' },
        al: { code: 'al', name: 'Albania (Shqipëri)', dialCode: 355, example: '066 123 4567' },
        dz: { code: 'dz', name: 'Algeria (‫الجزائر‬‎)', dialCode: 213, example: '0551 23 45 67' },
        as: { code: 'as', name: 'American Samoa', dialCode: 1684, example: '(684) 733-1234' },
        ad: { code: 'ad', name: 'Andorra', dialCode: 376, example: '312 345' },
        ao: { code: 'ao', name: 'Angola', dialCode: 244, example: '923 123 456' },
        ai: { code: 'ai', name: 'Anguilla', dialCode: 1264, example: '(264) 235-1234' },
        ag: { code: 'ag', name: 'Antigua and Barbuda', dialCode: 1268, example: '(268) 464-1234' },
        ar: { code: 'ar', name: 'Argentina', dialCode: 54, example: '011 15-2345-6789' },
        am: { code: 'am', name: 'Armenia (Հայաստան)', dialCode: 374, example: '077 123456' },
        aw: { code: 'aw', name: 'Aruba', dialCode: 297, example: '560 1234' },
        au: { code: 'au', name: 'Australia', dialCode: 61, example: '0412 345 678' },
        at: { code: 'at', name: 'Austria (Österreich)', dialCode: 43, example: '0664 123456' },
        az: { code: 'az', name: 'Azerbaijan (Azərbaycan)', dialCode: 994, example: '040 123 45 67' },
        bs: { code: 'bs', name: 'Bahamas', dialCode: 1242, example: '(242) 359-1234' },
        bh: { code: 'bh', name: 'Bahrain (‫البحرين‬‎)', dialCode: 973, example: '3600 1234' },
        bd: { code: 'bd', name: 'Bangladesh (বাংলাদেশ)', dialCode: 880, example: '01812-345678' },
        bb: { code: 'bb', name: 'Barbados', dialCode: 1246, example: '(246) 250-1234' },
        by: { code: 'by', name: 'Belarus (Беларусь)', dialCode: 375, example: '8 029 491-19-11' },
        be: { code: 'be', name: 'Belgium (België)', dialCode: 32, example: '0470 12 34 56' },
        bz: { code: 'bz', name: 'Belize', dialCode: 501, example: '622-1234' },
        bj: { code: 'bj', name: 'Benin (Bénin)', dialCode: 229, example: '90 01 12 34' },
        bm: { code: 'bm', name: 'Bermuda', dialCode: 1441, example: '(441) 370-1234' },
        bt: { code: 'bt', name: 'Bhutan (འབྲུག)', dialCode: 975, example: '17 12 34 56' },
        bo: { code: 'bo', name: 'Bolivia', dialCode: 591, example: '71234567' },
        ba: { code: 'ba', name: 'Bosnia and Herzegovina (Босна и Херцеговина)', dialCode: 387, example: '061 123 456' },
        bw: { code: 'bw', name: 'Botswana', dialCode: 267, example: '71 123 456' },
        br: { code: 'br', name: 'Brazil (Brasil)', dialCode: 55, example: '(11) 96123-4567' },
        io: { code: 'io', name: 'British Indian Ocean Territory', dialCode: 246, example: '380 1234' },
        vg: { code: 'vg', name: 'British Virgin Islands', dialCode: 1284, example: '(284) 300-1234' },
        bn: { code: 'bn', name: 'Brunei', dialCode: 673, example: '712 3456' },
        bg: { code: 'bg', name: 'Bulgaria (България)', dialCode: 359, example: '048 123 456' },
        bf: { code: 'bf', name: 'Burkina Faso', dialCode: 226, example: '70 12 34 56' },
        bi: { code: 'bi', name: 'Burundi (Uburundi)', dialCode: 257, example: '79 56 12 34' },
        kh: { code: 'kh', name: 'Cambodia (កម្ពុជា)', dialCode: 855, example: '091 234 567' },
        cm: { code: 'cm', name: 'Cameroon (Cameroun)', dialCode: 237, example: '6 71 23 45 67' },
        ca: { code: 'ca', name: 'Canada', dialCode: 1, example: '(204) 234-5678' },
        cv: { code: 'cv', name: 'Cape Verde (Kabu Verdi)', dialCode: 238, example: '991 12 34' },
        bq: { code: 'bq', name: 'Caribbean Netherlands', dialCode: 599, example: '318 1234' },
        ky: { code: 'ky', name: 'Cayman Islands', dialCode: 1345, example: '(345) 323-1234' },
        cf: { code: 'cf', name: 'Central African Republic (République centrafricaine)', dialCode: 236, example: '70 01 23 45' },
        td: { code: 'td', name: 'Chad (Tchad)', dialCode: 235, example: '63 01 23 45' },
        cl: { code: 'cl', name: 'Chile', dialCode: 56, example: '09 6123 4567' },
        cn: { code: 'cn', name: 'China (中国)', dialCode: 86, example: '131 2345 6789' },
        cx: { code: 'cx', name: 'Christmas Island', dialCode: 61, example: '0412 345 678' },
        cc: { code: 'cc', name: 'Cocos (Keeling) Islands', dialCode: 61, example: '0412 345 678' },
        co: { code: 'co', name: 'Colombia', dialCode: 57, example: '321 1234567' },
        km: { code: 'km', name: 'Comoros (‫جزر القمر‬‎)', dialCode: 269, example: '321 23 45' },
        cd: { code: 'cd', name: 'Congo (DRC) (Jamhuri ya Kidemokrasia ya Kongo)', dialCode: 243, example: '0991 234 567' },
        cg: { code: 'cg', name: 'Congo (Republic) (Congo-Brazzaville)', dialCode: 242, example: '06 123 4567' },
        ck: { code: 'ck', name: 'Cook Islands', dialCode: 682, example: '71 234' },
        cr: { code: 'cr', name: 'Costa Rica', dialCode: 506, example: '8312 3456' },
        ci: { code: 'ci', name: 'Côte d’Ivoire', dialCode: 225, example: '01 23 45 67' },
        hr: { code: 'hr', name: 'Croatia (Hrvatska)', dialCode: 385, example: '091 234 5678' },
        cu: { code: 'cu', name: 'Cuba', dialCode: 53, example: '05 1234567' },
        cw: { code: 'cw', name: 'Curaçao', dialCode: 599, example: '9 518 1234' },
        cy: { code: 'cy', name: 'Cyprus (Κύπρος)', dialCode: 357, example: '96 123456' },
        cz: { code: 'cz', name: 'Czech Republic (Česká republika)', dialCode: 420, example: '601 123 456' },
        dk: { code: 'dk', name: 'Denmark (Danmark)', dialCode: 45, example: '20 12 34 56' },
        dj: { code: 'dj', name: 'Djibouti', dialCode: 253, example: '77 83 10 01' },
        dm: { code: 'dm', name: 'Dominica', dialCode: 1767, example: '(767) 225-1234' },
        do: { code: 'do', name: 'Dominican Republic (República Dominicana)', dialCode: 1, example: '(809) 234-5678' },
        ec: { code: 'ec', name: 'Ecuador', dialCode: 593, example: '099 123 4567' },
        eg: { code: 'eg', name: 'Egypt (‫مصر‬‎)', dialCode: 20, example: '0100 123 4567' },
        sv: { code: 'sv', name: 'El Salvador', dialCode: 503, example: '7012 3456' },
        gq: { code: 'gq', name: 'Equatorial Guinea (Guinea Ecuatorial)', dialCode: 240, example: '222 123 456' },
        er: { code: 'er', name: 'Eritrea', dialCode: 291, example: '07 123 456' },
        ee: { code: 'ee', name: 'Estonia (Eesti)', dialCode: 372, example: '5123 4567' },
        et: { code: 'et', name: 'Ethiopia', dialCode: 251, example: '091 123 4567' },
        fk: { code: 'fk', name: 'Falkland Islands (Islas Malvinas)', dialCode: 500, example: '51234' },
        fo: { code: 'fo', name: 'Faroe Islands (Føroyar)', dialCode: 298, example: '211234' },
        fj: { code: 'fj', name: 'Fiji', dialCode: 679, example: '701 2345' },
        fi: { code: 'fi', name: 'Finland (Suomi)', dialCode: 358, example: '041 2345678' },
        fr: { code: 'fr', name: 'France', dialCode: 33, example: '06 12 34 56 78' },
        gf: { code: 'gf', name: 'French Guiana (Guyane française)', dialCode: 594, example: '0694 20 12 34' },
        pf: { code: 'pf', name: 'French Polynesia (Polynésie française)', dialCode: 689, example: '87 12 34 56' },
        ga: { code: 'ga', name: 'Gabon', dialCode: 241, example: '06 03 12 34' },
        gm: { code: 'gm', name: 'Gambia', dialCode: 220, example: '301 2345' },
        ge: { code: 'ge', name: 'Georgia (საქართველო)', dialCode: 995, example: '555 12 34 56' },
        de: { code: 'de', name: 'Germany (Deutschland)', dialCode: 49, example: '01512 3456789' },
        gh: { code: 'gh', name: 'Ghana (Gaana)', dialCode: 233, example: '023 123 4567' },
        gi: { code: 'gi', name: 'Gibraltar', dialCode: 350, example: '57123456' },
        gr: { code: 'gr', name: 'Greece (Ελλάδα)', dialCode: 30, example: '691 234 5678' },
        gl: { code: 'gl', name: 'Greenland (Kalaallit Nunaat)', dialCode: 299, example: '22 12 34' },
        gd: { code: 'gd', name: 'Grenada', dialCode: 1473, example: '(473) 403-1234' },
        gp: { code: 'gp', name: 'Guadeloupe', dialCode: 590, example: '0690 30-1234' },
        gu: { code: 'gu', name: 'Guam', dialCode: 1671, example: '(671) 300-1234' },
        gt: { code: 'gt', name: 'Guatemala', dialCode: 502, example: '5123 4567' },
        gg: { code: 'gg', name: 'Guernsey', dialCode: 44, example: '07781 123456' },
        gn: { code: 'gn', name: 'Guinea (Guinée)', dialCode: 224, example: '601 12 34 56' },
        gw: { code: 'gw', name: 'Guinea-Bissau (Guiné Bissau)', dialCode: 245, example: '955 012 345' },
        gy: { code: 'gy', name: 'Guyana', dialCode: 592, example: '609 1234' },
        ht: { code: 'ht', name: 'Haiti', dialCode: 509, example: '34 10 1234' },
        hn: { code: 'hn', name: 'Honduras', dialCode: 504, example: '9123-4567' },
        hk: { code: 'hk', name: 'Hong Kong (香港)', dialCode: 852, example: '5123 4567' },
        hu: { code: 'hu', name: 'Hungary (Magyarország)', dialCode: 36, example: '(20) 123 4567' },
        is: { code: 'is', name: 'Iceland (Ísland)', dialCode: 354, example: '611 1234' },
        in: { code: 'in', name: 'India (भारत)', dialCode: 91, example: '099876 54321' },
        id: { code: 'id', name: 'Indonesia', dialCode: 62, example: '0812-345-678' },
        ir: { code: 'ir', name: 'Iran (‫ایران‬‎)', dialCode: 98, example: '0912 345 6789' },
        iq: { code: 'iq', name: 'Iraq (‫العراق‬‎)', dialCode: 964, example: '0791 234 5678' },
        ie: { code: 'ie', name: 'Ireland', dialCode: 353, example: '085 012 3456' },
        im: { code: 'im', name: 'Isle of Man', dialCode: 44, example: '07924 123456' },
        il: { code: 'il', name: 'Israel (‫ישראל‬‎)', dialCode: 972, example: '050-123-4567' },
        it: { code: 'it', name: 'Italy (Italia)', dialCode: 39, example: '312 345 6789' },
        jm: { code: 'jm', name: 'Jamaica', dialCode: 1876, example: '(876) 210-1234' },
        jp: { code: 'jp', name: 'Japan (日本)', dialCode: 81, example: '090-1234-5678' },
        je: { code: 'je', name: 'Jersey', dialCode: 44, example: '07797 123456' },
        jo: { code: 'jo', name: 'Jordan (‫الأردن‬‎)', dialCode: 962, example: '07 9012 3456' },
        kz: { code: 'kz', name: 'Kazakhstan (Казахстан)', dialCode: 7, example: '8 (771) 000 9998' },
        ke: { code: 'ke', name: 'Kenya', dialCode: 254, example: '0712 123456' },
        ki: { code: 'ki', name: 'Kiribati', dialCode: 686, example: '72012345' },
        xk: { code: 'xk', name: 'Kosovo', dialCode: 383, example: '' },
        kw: { code: 'kw', name: 'Kuwait (‫الكويت‬‎)', dialCode: 965, example: '500 12345' },
        kg: { code: 'kg', name: 'Kyrgyzstan (Кыргызстан)', dialCode: 996, example: '0700 123 456' },
        la: { code: 'la', name: 'Laos (ລາວ)', dialCode: 856, example: '020 23 123 456' },
        lv: { code: 'lv', name: 'Latvia (Latvija)', dialCode: 371, example: '21 234 567' },
        lb: { code: 'lb', name: 'Lebanon (‫لبنان‬‎)', dialCode: 961, example: '71 123 456' },
        ls: { code: 'ls', name: 'Lesotho', dialCode: 266, example: '5012 3456' },
        lr: { code: 'lr', name: 'Liberia', dialCode: 231, example: '077 012 3456' },
        ly: { code: 'ly', name: 'Libya (‫ليبيا‬‎)', dialCode: 218, example: '091-2345678' },
        li: { code: 'li', name: 'Liechtenstein', dialCode: 423, example: '660 234 567' },
        lt: { code: 'lt', name: 'Lithuania (Lietuva)', dialCode: 370, example: '(8-612) 34567' },
        lu: { code: 'lu', name: 'Luxembourg', dialCode: 352, example: '628 123 456' },
        mo: { code: 'mo', name: 'Macau (澳門)', dialCode: 853, example: '6612 3456' },
        mk: { code: 'mk', name: 'Macedonia (FYROM) (Македонија)', dialCode: 389, example: '072 345 678' },
        mg: { code: 'mg', name: 'Madagascar (Madagasikara)', dialCode: 261, example: '032 12 345 67' },
        mw: { code: 'mw', name: 'Malawi', dialCode: 265, example: '0991 23 45 67' },
        my: { code: 'my', name: 'Malaysia', dialCode: 60, example: '012-345 6789' },
        mv: { code: 'mv', name: 'Maldives', dialCode: 960, example: '771-2345' },
        ml: { code: 'ml', name: 'Mali', dialCode: 223, example: '65 01 23 45' },
        mt: { code: 'mt', name: 'Malta', dialCode: 356, example: '9696 1234' },
        mh: { code: 'mh', name: 'Marshall Islands', dialCode: 692, example: '235-1234' },
        mq: { code: 'mq', name: 'Martinique', dialCode: 596, example: '0696 20 12 34' },
        mr: { code: 'mr', name: 'Mauritania (‫موريتانيا‬‎)', dialCode: 222, example: '22 12 34 56' },
        mu: { code: 'mu', name: 'Mauritius (Moris)', dialCode: 230, example: '5251 2345' },
        yt: { code: 'yt', name: 'Mayotte', dialCode: 262, example: '0639 12 34 56' },
        mx: { code: 'mx', name: 'Mexico (México)', dialCode: 52, example: '044 222 123 4567' },
        fm: { code: 'fm', name: 'Micronesia', dialCode: 691, example: '350 1234' },
        md: { code: 'md', name: 'Moldova (Republica Moldova)', dialCode: 373, example: '0621 12 345' },
        mc: { code: 'mc', name: 'Monaco', dialCode: 377, example: '06 12 34 56 78' },
        mn: { code: 'mn', name: 'Mongolia (Монгол)', dialCode: 976, example: '8812 3456' },
        me: { code: 'me', name: 'Montenegro (Crna Gora)', dialCode: 382, example: '067 622 901' },
        ms: { code: 'ms', name: 'Montserrat', dialCode: 1664, example: '(664) 492-3456' },
        ma: { code: 'ma', name: 'Morocco (‫المغرب‬‎)', dialCode: 212, example: '0650-123456' },
        mz: { code: 'mz', name: 'Mozambique (Moçambique)', dialCode: 258, example: '82 123 4567' },
        mm: { code: 'mm', name: 'Myanmar (Burma) (မြန်မာ)', dialCode: 95, example: '09 212 3456' },
        na: { code: 'na', name: 'Namibia (Namibië)', dialCode: 264, example: '081 123 4567' },
        nr: { code: 'nr', name: 'Nauru', dialCode: 674, example: '555 1234' },
        np: { code: 'np', name: 'Nepal (नेपाल)', dialCode: 977, example: '984-1234567' },
        nl: { code: 'nl', name: 'Netherlands (Nederland)', dialCode: 31, example: '06 12345678' },
        nc: { code: 'nc', name: 'New Caledonia (Nouvelle-Calédonie)', dialCode: 687, example: '75.12.34' },
        nz: { code: 'nz', name: 'New Zealand', dialCode: 64, example: '021 123 4567' },
        ni: { code: 'ni', name: 'Nicaragua', dialCode: 505, example: '8123 4567' },
        ne: { code: 'ne', name: 'Niger (Nijar)', dialCode: 227, example: '93 12 34 56' },
        ng: { code: 'ng', name: 'Nigeria', dialCode: 234, example: '0802 123 4567' },
        nu: { code: 'nu', name: 'Niue', dialCode: 683, example: '1234' },
        nf: { code: 'nf', name: 'Norfolk Island', dialCode: 672, example: '3 81234' },
        kp: { code: 'kp', name: 'North Korea (조선 민주주의 인민 공화국)', dialCode: 850, example: '0192 123 4567' },
        mp: { code: 'mp', name: 'Northern Mariana Islands', dialCode: 1670, example: '(670) 234-5678' },
        no: { code: 'no', name: 'Norway (Norge)', dialCode: 47, example: '406 12 345' },
        om: { code: 'om', name: 'Oman (‫عُمان‬‎)', dialCode: 968, example: '9212 3456' },
        pk: { code: 'pk', name: 'Pakistan (‫پاکستان‬‎)', dialCode: 92, example: '0301 2345678' },
        pw: { code: 'pw', name: 'Palau', dialCode: 680, example: '620 1234' },
        ps: { code: 'ps', name: 'Palestine (‫فلسطين‬‎)', dialCode: 970, example: '0599 123 456' },
        pa: { code: 'pa', name: 'Panama (Panamá)', dialCode: 507, example: '6001-2345' },
        pg: { code: 'pg', name: 'Papua New Guinea', dialCode: 675, example: '681 2345' },
        py: { code: 'py', name: 'Paraguay', dialCode: 595, example: '0961 456789' },
        pe: { code: 'pe', name: 'Peru (Perú)', dialCode: 51, example: '912 345 678' },
        ph: { code: 'ph', name: 'Philippines', dialCode: 63, example: '0905 123 4567' },
        pl: { code: 'pl', name: 'Poland (Polska)', dialCode: 48, example: '512 345 678' },
        pt: { code: 'pt', name: 'Portugal', dialCode: 351, example: '912 345 678' },
        pr: { code: 'pr', name: 'Puerto Rico', dialCode: 1, example: '(787) 234-5678' },
        qa: { code: 'qa', name: 'Qatar (‫قطر‬‎)', dialCode: 974, example: '3312 3456' },
        re: { code: 're', name: 'Réunion (La Réunion)', dialCode: 262, example: '0692 12 34 56' },
        ro: { code: 'ro', name: 'Romania (România)', dialCode: 40, example: '0712 345 678' },
        ru: { code: 'ru', name: 'Russia (Россия)', dialCode: 7, example: '8 (912) 345-67-89' },
        rw: { code: 'rw', name: 'Rwanda', dialCode: 250, example: '0720 123 456' },
        bl: { code: 'bl', name: 'Saint Barthélemy (Saint-Barthélemy)', dialCode: 590, example: '0690 30-1234' },
        sh: { code: 'sh', name: 'Saint Helena', dialCode: 290, example: '51234' },
        kn: { code: 'kn', name: 'Saint Kitts and Nevis', dialCode: 1869, example: '(869) 765-2917' },
        lc: { code: 'lc', name: 'Saint Lucia', dialCode: 1758, example: '(758) 284-5678' },
        mf: { code: 'mf', name: 'Saint Martin (Saint-Martin (partie française))', dialCode: 590, example: '0690 30-1234' },
        pm: { code: 'pm', name: 'Saint Pierre and Miquelon (Saint-Pierre-et-Miquelon)', dialCode: 508, example: '055 12 34' },
        vc: { code: 'vc', name: 'Saint Vincent and the Grenadines', dialCode: 1784, example: '(784) 430-1234' },
        ws: { code: 'ws', name: 'Samoa', dialCode: 685, example: '601234' },
        sm: { code: 'sm', name: 'San Marino', dialCode: 378, example: '66 66 12 12' },
        st: { code: 'st', name: 'São Tomé and Príncipe (São Tomé e Príncipe)', dialCode: 239, example: '981 2345' },
        sa: { code: 'sa', name: 'Saudi Arabia (‫المملكة العربية السعودية‬‎)', dialCode: 966, example: '051 234 5678' },
        sn: { code: 'sn', name: 'Senegal (Sénégal)', dialCode: 221, example: '70 123 45 67' },
        rs: { code: 'rs', name: 'Serbia (Србија)', dialCode: 381, example: '060 1234567' },
        sc: { code: 'sc', name: 'Seychelles', dialCode: 248, example: '2 510 123' },
        sl: { code: 'sl', name: 'Sierra Leone', dialCode: 232, example: '(025) 123456' },
        sg: { code: 'sg', name: 'Singapore', dialCode: 65, example: '8123 4567' },
        sx: { code: 'sx', name: 'Sint Maarten', dialCode: 1721, example: '(721) 520-5678' },
        sk: { code: 'sk', name: 'Slovakia (Slovensko)', dialCode: 421, example: '0912 123 456' },
        si: { code: 'si', name: 'Slovenia (Slovenija)', dialCode: 386, example: '031 234 567' },
        sb: { code: 'sb', name: 'Solomon Islands', dialCode: 677, example: '74 21234' },
        so: { code: 'so', name: 'Somalia (Soomaaliya)', dialCode: 252, example: '7 1123456' },
        za: { code: 'za', name: 'South Africa', dialCode: 27, example: '071 123 4567' },
        kr: { code: 'kr', name: 'South Korea (대한민국)', dialCode: 82, example: '010-0000-0000' },
        ss: { code: 'ss', name: 'South Sudan (‫جنوب السودان‬‎)', dialCode: 211, example: '0977 123 456' },
        es: { code: 'es', name: 'Spain (España)', dialCode: 34, example: '612 34 56 78' },
        lk: { code: 'lk', name: 'Sri Lanka (ශ්‍රී ලංකාව)', dialCode: 94, example: '071 234 5678' },
        sd: { code: 'sd', name: 'Sudan (‫السودان‬‎)', dialCode: 249, example: '091 123 1234' },
        sr: { code: 'sr', name: 'Suriname', dialCode: 597, example: '741-2345' },
        sj: { code: 'sj', name: 'Svalbard and Jan Mayen', dialCode: 47, example: '412 34 567' },
        sz: { code: 'sz', name: 'Swaziland', dialCode: 268, example: '7612 3456' },
        se: { code: 'se', name: 'Sweden (Sverige)', dialCode: 46, example: '070-123 45 67' },
        ch: { code: 'ch', name: 'Switzerland (Schweiz)', dialCode: 41, example: '078 123 45 67' },
        sy: { code: 'sy', name: 'Syria (‫سوريا‬‎)', dialCode: 963, example: '0944 567 890' },
        tw: { code: 'tw', name: 'Taiwan (台灣)', dialCode: 886, example: '0912 345 678' },
        tj: { code: 'tj', name: 'Tajikistan', dialCode: 992, example: '(8) 917 12 3456' },
        tz: { code: 'tz', name: 'Tanzania', dialCode: 255, example: '0621 234 567' },
        th: { code: 'th', name: 'Thailand (ไทย)', dialCode: 66, example: '081 234 5678' },
        tl: { code: 'tl', name: 'Timor-Leste', dialCode: 670, example: '7721 2345' },
        tg: { code: 'tg', name: 'Togo', dialCode: 228, example: '90 11 23 45' },
        tk: { code: 'tk', name: 'Tokelau', dialCode: 690, example: '7290' },
        to: { code: 'to', name: 'Tonga', dialCode: 676, example: '771 5123' },
        tt: { code: 'tt', name: 'Trinidad and Tobago', dialCode: 1868, example: '(868) 291-1234' },
        tn: { code: 'tn', name: 'Tunisia (‫تونس‬‎)', dialCode: 216, example: '20 123 456' },
        tr: { code: 'tr', name: 'Turkey (Türkiye)', dialCode: 90, example: '0501 234 56 78' },
        tm: { code: 'tm', name: 'Turkmenistan', dialCode: 993, example: '8 66 123456' },
        tc: { code: 'tc', name: 'Turks and Caicos Islands', dialCode: 1649, example: '(649) 231-1234' },
        tv: { code: 'tv', name: 'Tuvalu', dialCode: 688, example: '901234' },
        us: { code: 'us', name: 'United States', dialCode: 1, example: '(201) 555-0123' },
        gb: { code: 'gb', name: 'United Kingdom', dialCode: 44, example: '07400 123456' },
        vi: { code: 'vi', name: 'U.S. Virgin Islands', dialCode: 1340, example: '(340) 642-1234' },
        ug: { code: 'ug', name: 'Uganda', dialCode: 256, example: '0712 345678' },
        ua: { code: 'ua', name: 'Ukraine (Україна)', dialCode: 380, example: '039 123 4567' },
        ae: { code: 'ae', name: 'United Arab Emirates (‫الإمارات العربية المتحدة‬‎)', dialCode: 971, example: '050 123 4567' },
        uy: { code: 'uy', name: 'Uruguay', dialCode: 598, example: '094 231 234' },
        uz: { code: 'uz', name: 'Uzbekistan (Oʻzbekiston)', dialCode: 998, example: '8 91 234 56 78' },
        vu: { code: 'vu', name: 'Vanuatu', dialCode: 678, example: '591 2345' },
        va: { code: 'va', name: 'Vatican City (Città del Vaticano)', dialCode: 39, example: '312 345 6789' },
        ve: { code: 've', name: 'Venezuela', dialCode: 58, example: '0412-1234567' },
        vn: { code: 'vn', name: 'Vietnam (Việt Nam)', dialCode: 84, example: '091 234 56 78' },
        wf: { code: 'wf', name: 'Wallis and Futuna', dialCode: 681, example: '50 12 34' },
        eh: { code: 'eh', name: 'Western Sahara (‫الصحراء الغربية‬‎)', dialCode: 212, example: '0650-123456' },
        ye: { code: 'ye', name: 'Yemen (‫اليمن‬‎)', dialCode: 967, example: '0712 345 678' },
        zm: { code: 'zm', name: 'Zambia', dialCode: 260, example: '095 5123456' },
        zw: { code: 'zw', name: 'Zimbabwe', dialCode: 263, example: '071 123 4567' },
        ax: { code: 'ax', name: 'Åland Islands', dialCode: 358, example: '041 2345678' },
    };

  /* eslint-enable object-curly-newline, no-unused-vars */

  let counter = 0;

  export default {
    name: 'phone-input',
    props: {
        phone: {
            type: Object,
        },
      name: {
        type: String,
        default() {
          counter += 1;
          return `tel-input-${counter}`;
        },
      },
      value: {
        type: [
          Number,
          String,
        ],
        default: '',
      },
      availbleOnly: {
        type: Array,
        default: null,
      },
      toFront: {
        type: Array,
        default() {
          return [];
        },
      },
      code: {
        type: String,
        default: null,
      },
      onChange: {
        type: Function,
        default: null,
      },
      onInit: {
        type: Function,
        default: null,
      },
    },

    data() {
      const { code, availableOnly, value } = this;
      let tmpPhonesData = Object.assign({}, phonesData);

      if (isArray(availableOnly)) {
        tmpPhonesData = {};
        availableOnly.forEach((item) => {
          if (has.call(phonesData, item)) {
            tmpPhonesData[item] = phonesData[item];
          }
        });
      }

      this.availableData = tmpPhonesData;
      const needCode = has.call(this.availableData, code) ? code : Object.keys(this.availableData)[0];

      if (!needCode) {
        throw new Error('Available data is empty.Please set correct "availableOnly" attribute [vue-phone-input]');
      }

      return {
        // phoneNumber: value,
        countryCode: needCode,
        isVisiblePanel: false,
      };
    },

    computed: {
      intlData() {
        return this.availableData[this.countryCode];
      },

      itemsData() {
        const frontItems = [];
        const otherItems = [];
        const phonesDataCopy = Object.assign({}, this.availableData);

        this.toFront.forEach((code) => {
          const item = phonesDataCopy[code];

          if (isObject(item)) {
            frontItems.push(item);
            delete phonesDataCopy[code];
          }
        });

        Object.values(phonesDataCopy).forEach(item => otherItems.push(item));
        const needSeparator = frontItems.length > 0 && otherItems.length > 0;

        return {
          phoneNumber: this.phone.number,
          phoneCode: this.phone.code,
          frontItems,
          otherItems,
          needSeparator,
        };
      },
    },

    methods: {
      handleChangePhoneNumber(event) {
        this.phone.number = event.target.value;

        if (isFunction(this.onChange)) {
          this.onChange(this);
        }
      },

      setCode(code) {
        this.countryCode = code;
        let vm = this;

        Object.keys(phonesData).forEach((key, index) => {
            let phoneData = phonesData[key];
            if (phoneData.code == code) {
                vm.phone.code = phoneData.dialCode;
            }
        });

        this.hideList();
      },

      showList() {
        this.isVisiblePanel = true;
      },

      hideList() {
        this.isVisiblePanel = false;
      },

      toggeList() {
        (this.isVisiblePanel ? this.hideList : this.showList)();
      },

      changeCountry() {
        if(this.phone.number.length > 1 && this.phone.number.startsWith('+')){
            Object.keys(phonesData).forEach((key, index) => {
                
                let phoneData = phonesData[key];
                if (this.phone.number.startsWith('+' + phoneData.dialCode) || this.phone.number.startsWith(phoneData.dialCode)) {
                    this.setCode(phoneData.code);
                    this.phone.number = '';
                    this.phone.code = phoneData.dialCode;
                }
            });
        }

      }
    },

    mounted() {
      if (isFunction(this.onInit)) {
        this.onInit(this);
      }
    },
  };
</script>

<style lang="less">
    .intl-phone-input {
        position:relative;
        display:inline-block;
        font-family: Arial, Helvetica, sans-serif;

        input[type="text"] {
            display: block;
        width: 100%;
        height: 34px;
        padding: 6px 12px;
        font-size: 14px;
        line-height: 1.42857143;
        color: #555;
        background-color: #fff;
        background-image: none;
        border: 1px solid #ccc;
        border-radius: 4px;
        -webkit-box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
        box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
        -webkit-transition: border-color ease-in-out .15s,-webkit-box-shadow ease-in-out .15s;
        -o-transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
        transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
        }
    }

    .intl-phone-input * {
        box-sizing:border-box;
        -moz-box-sizing:border-box;
    }

    .intl-phone-input .hide {
        display:none;
    }

    .intl-phone-input .v-hide {
        visibility:hidden;
    }

    .intl-phone-input input,
    .intl-phone-input input[type=text],
    .intl-phone-input input[type=tel] {
        position:relative;
        z-index:0;
        margin-top:0 !important;
        margin-bottom:0 !important;
        padding-right:36px;
        margin-right:0;
    }

    .intl-phone-input .flag-container {
        position:absolute;
        top:0;
        bottom:0;
        right:0;
        padding:1px;
    }

    .intl-phone-input .selected-flag {
        z-index:1;
        position:relative;
        width:36px;
        height:100%;
        padding:0 0 0 8px;
    }

    .intl-phone-input .selected-flag .iti-flag {
        position:absolute;
        top:0;
        bottom:0;
        margin:auto;
    }

    .intl-phone-input .selected-flag .iti-arrow {
        position:absolute;
        top:50%;
        margin-top:-2px;
        right:6px;
        width:0;
        height:0;
        border-left:3px solid transparent;
        border-right:3px solid transparent;
        border-top:4px solid #555;
    }

    .intl-phone-input .selected-flag .iti-arrow.up {
        border-top:none;
        border-bottom:4px solid #555;
    }

    .intl-phone-input .country-list {
        position:absolute;
        z-index:2;
        list-style:none;
        text-align:left;
        padding:0;
        margin:0 0 0 -1px;
        box-shadow:1px 1px 4px rgba(0,0,0,0.2);
        background-color:white;
        border:1px solid #CCC;
        white-space:nowrap;
        max-height:200px;
        overflow-y:scroll;
    }

    .intl-phone-input .country-list.dropup {
        bottom:100%;
        margin-bottom:-1px;
    }

    .intl-phone-input .country-list .flag-box {
        display:inline-block;
        width:20px;
    }

    @media (max-width: 500px) {
        .intl-phone-input .country-list {
            white-space:normal;
        }

    }

    .intl-phone-input .country-list .divider {
        padding-bottom:5px;
        margin-bottom:5px;
        border-bottom:1px solid #CCC;
    }

    .intl-phone-input .country-list .country {
        padding:5px 10px;
    }

    .intl-phone-input .country-list .country .dial-code {
        color:#999;
    }

    .intl-phone-input .country-list .country.highlight {
        background-color:rgba(0,0,0,0.05);
    }

    .intl-phone-input .country-list .flag-box,
    .intl-phone-input .country-list .country-name,
    .intl-phone-input .country-list .dial-code {
        vertical-align:middle;
    }

    .intl-phone-input .country-list .flag-box,
    .intl-phone-input .country-list .country-name {
        margin-right:6px;
    }

    .intl-phone-input.allow-dropdown input,
    .intl-phone-input.allow-dropdown input[type=text],
    .intl-phone-input.allow-dropdown input[type=tel],
    .intl-phone-input.separate-dial-code input,
    .intl-phone-input.separate-dial-code input[type=text],
    .intl-phone-input.separate-dial-code input[type=tel] {
        padding-right:6px;
        padding-left:52px;
        margin-left:0;
    }

    .intl-phone-input.allow-dropdown .flag-container,
    .intl-phone-input.separate-dial-code .flag-container {
        right:auto;
        left:0;
    }

    .intl-phone-input.allow-dropdown .selected-flag,
    .intl-phone-input.separate-dial-code .selected-flag {
        width:46px;
    }

    .intl-phone-input.allow-dropdown .flag-container:hover {
        cursor:pointer;
    }

    .intl-phone-input.allow-dropdown .flag-container:hover .selected-flag {
        background-color:rgba(0,0,0,0.05);
    }

    .intl-phone-input.allow-dropdown input[disabled]+.flag-container:hover,
    .intl-phone-input.allow-dropdown input[readonly]+.flag-container:hover {
        cursor:default;
    }

    .intl-phone-input.allow-dropdown input[disabled]+.flag-container:hover .selected-flag,
    .intl-phone-input.allow-dropdown input[readonly]+.flag-container:hover .selected-flag {
        background-color:transparent;
    }

    .intl-phone-input.separate-dial-code .selected-flag {
        background-color:rgba(0,0,0,0.05);
        display:table;
    }

    .intl-phone-input.separate-dial-code .selected-dial-code {
        display:table-cell;
        vertical-align:middle;
        padding-left:28px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-2 input,
    .intl-phone-input.separate-dial-code.iti-sdc-2 input[type=text],
    .intl-phone-input.separate-dial-code.iti-sdc-2 input[type=tel] {
        padding-left:66px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-2 .selected-flag {
        width:60px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-2 input,
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-2 input[type=text],
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-2 input[type=tel] {
        padding-left:76px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-2 .selected-flag {
        width:70px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-3 input,
    .intl-phone-input.separate-dial-code.iti-sdc-3 input[type=text],
    .intl-phone-input.separate-dial-code.iti-sdc-3 input[type=tel] {
        padding-left:74px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-3 .selected-flag {
        width:68px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-3 input,
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-3 input[type=text],
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-3 input[type=tel] {
        padding-left:84px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-3 .selected-flag {
        width:78px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-4 input,
    .intl-phone-input.separate-dial-code.iti-sdc-4 input[type=text],
    .intl-phone-input.separate-dial-code.iti-sdc-4 input[type=tel] {
        padding-left:82px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-4 .selected-flag {
        width:76px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-4 input,
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-4 input[type=text],
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-4 input[type=tel] {
        padding-left:92px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-4 .selected-flag {
        width:86px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-5 input,
    .intl-phone-input.separate-dial-code.iti-sdc-5 input[type=text],
    .intl-phone-input.separate-dial-code.iti-sdc-5 input[type=tel] {
        padding-left:90px;
    }

    .intl-phone-input.separate-dial-code.iti-sdc-5 .selected-flag {
        width:84px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-5 input,
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-5 input[type=text],
    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-5 input[type=tel] {
        padding-left:100px;
    }

    .intl-phone-input.separate-dial-code.allow-dropdown.iti-sdc-5 .selected-flag {
        width:94px;
    }

    .intl-phone-input.iti-container {
        position:absolute;
        top:-1000px;
        left:-1000px;
        z-index:1060;
        padding:1px;
    }

    .intl-phone-input.iti-container:hover {
        cursor:pointer;
    }

    .iti-mobile .intl-phone-input.iti-container {
        top:30px;
        bottom:30px;
        left:30px;
        right:30px;
        position:fixed;
    }

    .iti-mobile .intl-phone-input .country-list {
        max-height:100%;
        width:100%;
    }

    .iti-mobile .intl-phone-input .country-list .country {
        padding:10px 10px;
        line-height:1.5em;
    }

    .iti-flag {
        width:20px;
    }

    .iti-flag.be {
        width:18px;
    }

    .iti-flag.ch {
        width:15px;
    }

    .iti-flag.mc {
        width:19px;
    }

    .iti-flag.ne {
        width:18px;
    }

    .iti-flag.np {
        width:13px;
    }

    .iti-flag.va {
        width:15px;
    }

    @media only screen and (-webkit-min-device-pixel-ratio: 2),
    only screen and (min--moz-device-pixel-ratio: 2),
    only screen and (-o-min-device-pixel-ratio: 2 / 1),
    only screen and (min-device-pixel-ratio: 2),
    only screen and (min-resolution: 192dpi),
    only screen and (min-resolution: 2dppx) {
        .iti-flag {
            background-size:5630px 15px
        }

    }

    .iti-flag.ac {
        height:10px;
        background-position:0px 0px;
    }

    .iti-flag.ad {
        height:14px;
        background-position:-22px 0px;
    }

    .iti-flag.ae {
        height:10px;
        background-position:-44px 0px;
    }

    .iti-flag.af {
        height:14px;
        background-position:-66px 0px;
    }

    .iti-flag.ag {
        height:14px;
        background-position:-88px 0px;
    }

    .iti-flag.ai {
        height:10px;
        background-position:-110px 0px;
    }

    .iti-flag.al {
        height:15px;
        background-position:-132px 0px;
    }

    .iti-flag.am {
        height:10px;
        background-position:-154px 0px;
    }

    .iti-flag.ao {
        height:14px;
        background-position:-176px 0px;
    }

    .iti-flag.aq {
        height:14px;
        background-position:-198px 0px;
    }

    .iti-flag.ar {
        height:13px;
        background-position:-220px 0px;
    }

    .iti-flag.as {
        height:10px;
        background-position:-242px 0px;
    }

    .iti-flag.at {
        height:14px;
        background-position:-264px 0px;
    }

    .iti-flag.au {
        height:10px;
        background-position:-286px 0px;
    }

    .iti-flag.aw {
        height:14px;
        background-position:-308px 0px;
    }

    .iti-flag.ax {
        height:13px;
        background-position:-330px 0px;
    }

    .iti-flag.az {
        height:10px;
        background-position:-352px 0px;
    }

    .iti-flag.ba {
        height:10px;
        background-position:-374px 0px;
    }

    .iti-flag.bb {
        height:14px;
        background-position:-396px 0px;
    }

    .iti-flag.bd {
        height:12px;
        background-position:-418px 0px;
    }

    .iti-flag.be {
        height:15px;
        background-position:-440px 0px;
    }

    .iti-flag.bf {
        height:14px;
        background-position:-460px 0px;
    }

    .iti-flag.bg {
        height:12px;
        background-position:-482px 0px;
    }

    .iti-flag.bh {
        height:12px;
        background-position:-504px 0px;
    }

    .iti-flag.bi {
        height:12px;
        background-position:-526px 0px;
    }

    .iti-flag.bj {
        height:14px;
        background-position:-548px 0px;
    }

    .iti-flag.bl {
        height:14px;
        background-position:-570px 0px;
    }

    .iti-flag.bm {
        height:10px;
        background-position:-592px 0px;
    }

    .iti-flag.bn {
        height:10px;
        background-position:-614px 0px;
    }

    .iti-flag.bo {
        height:14px;
        background-position:-636px 0px;
    }

    .iti-flag.bq {
        height:14px;
        background-position:-658px 0px;
    }

    .iti-flag.br {
        height:14px;
        background-position:-680px 0px;
    }

    .iti-flag.bs {
        height:10px;
        background-position:-702px 0px;
    }

    .iti-flag.bt {
        height:14px;
        background-position:-724px 0px;
    }

    .iti-flag.bv {
        height:15px;
        background-position:-746px 0px;
    }

    .iti-flag.bw {
        height:14px;
        background-position:-768px 0px;
    }

    .iti-flag.by {
        height:10px;
        background-position:-790px 0px;
    }

    .iti-flag.bz {
        height:14px;
        background-position:-812px 0px;
    }

    .iti-flag.ca {
        height:10px;
        background-position:-834px 0px;
    }

    .iti-flag.cc {
        height:10px;
        background-position:-856px 0px;
    }

    .iti-flag.cd {
        height:15px;
        background-position:-878px 0px;
    }

    .iti-flag.cf {
        height:14px;
        background-position:-900px 0px;
    }

    .iti-flag.cg {
        height:14px;
        background-position:-922px 0px;
    }

    .iti-flag.ch {
        height:15px;
        background-position:-944px 0px;
    }

    .iti-flag.ci {
        height:14px;
        background-position:-961px 0px;
    }

    .iti-flag.ck {
        height:10px;
        background-position:-983px 0px;
    }

    .iti-flag.cl {
        height:14px;
        background-position:-1005px 0px;
    }

    .iti-flag.cm {
        height:14px;
        background-position:-1027px 0px;
    }

    .iti-flag.cn {
        height:14px;
        background-position:-1049px 0px;
    }

    .iti-flag.co {
        height:14px;
        background-position:-1071px 0px;
    }

    .iti-flag.cp {
        height:14px;
        background-position:-1093px 0px;
    }

    .iti-flag.cr {
        height:12px;
        background-position:-1115px 0px;
    }

    .iti-flag.cu {
        height:10px;
        background-position:-1137px 0px;
    }

    .iti-flag.cv {
        height:12px;
        background-position:-1159px 0px;
    }

    .iti-flag.cw {
        height:14px;
        background-position:-1181px 0px;
    }

    .iti-flag.cx {
        height:10px;
        background-position:-1203px 0px;
    }

    .iti-flag.cy {
        height:13px;
        background-position:-1225px 0px;
    }

    .iti-flag.cz {
        height:14px;
        background-position:-1247px 0px;
    }

    .iti-flag.de {
        height:12px;
        background-position:-1269px 0px;
    }

    .iti-flag.dg {
        height:10px;
        background-position:-1291px 0px;
    }

    .iti-flag.dj {
        height:14px;
        background-position:-1313px 0px;
    }

    .iti-flag.dk {
        height:15px;
        background-position:-1335px 0px;
    }

    .iti-flag.dm {
        height:10px;
        background-position:-1357px 0px;
    }

    .iti-flag.do {
        height:13px;
        background-position:-1379px 0px;
    }

    .iti-flag.dz {
        height:14px;
        background-position:-1401px 0px;
    }

    .iti-flag.ea {
        height:14px;
        background-position:-1423px 0px;
    }

    .iti-flag.ec {
        height:14px;
        background-position:-1445px 0px;
    }

    .iti-flag.ee {
        height:13px;
        background-position:-1467px 0px;
    }

    .iti-flag.eg {
        height:14px;
        background-position:-1489px 0px;
    }

    .iti-flag.eh {
        height:10px;
        background-position:-1511px 0px;
    }

    .iti-flag.er {
        height:10px;
        background-position:-1533px 0px;
    }

    .iti-flag.es {
        height:14px;
        background-position:-1555px 0px;
    }

    .iti-flag.et {
        height:10px;
        background-position:-1577px 0px;
    }

    .iti-flag.eu {
        height:14px;
        background-position:-1599px 0px;
    }

    .iti-flag.fi {
        height:12px;
        background-position:-1621px 0px;
    }

    .iti-flag.fj {
        height:10px;
        background-position:-1643px 0px;
    }

    .iti-flag.fk {
        height:10px;
        background-position:-1665px 0px;
    }

    .iti-flag.fm {
        height:11px;
        background-position:-1687px 0px;
    }

    .iti-flag.fo {
        height:15px;
        background-position:-1709px 0px;
    }

    .iti-flag.fr {
        height:14px;
        background-position:-1731px 0px;
    }

    .iti-flag.ga {
        height:15px;
        background-position:-1753px 0px;
    }

    .iti-flag.gb {
        height:10px;
        background-position:-1775px 0px;
    }

    .iti-flag.gd {
        height:12px;
        background-position:-1797px 0px;
    }

    .iti-flag.ge {
        height:14px;
        background-position:-1819px 0px;
    }

    .iti-flag.gf {
        height:14px;
        background-position:-1841px 0px;
    }

    .iti-flag.gg {
        height:14px;
        background-position:-1863px 0px;
    }

    .iti-flag.gh {
        height:14px;
        background-position:-1885px 0px;
    }

    .iti-flag.gi {
        height:10px;
        background-position:-1907px 0px;
    }

    .iti-flag.gl {
        height:14px;
        background-position:-1929px 0px;
    }

    .iti-flag.gm {
        height:14px;
        background-position:-1951px 0px;
    }

    .iti-flag.gn {
        height:14px;
        background-position:-1973px 0px;
    }

    .iti-flag.gp {
        height:14px;
        background-position:-1995px 0px;
    }

    .iti-flag.gq {
        height:14px;
        background-position:-2017px 0px;
    }

    .iti-flag.gr {
        height:14px;
        background-position:-2039px 0px;
    }

    .iti-flag.gs {
        height:10px;
        background-position:-2061px 0px;
    }

    .iti-flag.gt {
        height:13px;
        background-position:-2083px 0px;
    }

    .iti-flag.gu {
        height:11px;
        background-position:-2105px 0px;
    }

    .iti-flag.gw {
        height:10px;
        background-position:-2127px 0px;
    }

    .iti-flag.gy {
        height:12px;
        background-position:-2149px 0px;
    }

    .iti-flag.hk {
        height:14px;
        background-position:-2171px 0px;
    }

    .iti-flag.hm {
        height:10px;
        background-position:-2193px 0px;
    }

    .iti-flag.hn {
        height:10px;
        background-position:-2215px 0px;
    }

    .iti-flag.hr {
        height:10px;
        background-position:-2237px 0px;
    }

    .iti-flag.ht {
        height:12px;
        background-position:-2259px 0px;
    }

    .iti-flag.hu {
        height:10px;
        background-position:-2281px 0px;
    }

    .iti-flag.ic {
        height:14px;
        background-position:-2303px 0px;
    }

    .iti-flag.id {
        height:14px;
        background-position:-2325px 0px;
    }

    .iti-flag.ie {
        height:10px;
        background-position:-2347px 0px;
    }

    .iti-flag.il {
        height:15px;
        background-position:-2369px 0px;
    }

    .iti-flag.im {
        height:10px;
        background-position:-2391px 0px;
    }

    .iti-flag.in {
        height:14px;
        background-position:-2413px 0px;
    }

    .iti-flag.io {
        height:10px;
        background-position:-2435px 0px;
    }

    .iti-flag.iq {
        height:14px;
        background-position:-2457px 0px;
    }

    .iti-flag.ir {
        height:12px;
        background-position:-2479px 0px;
    }

    .iti-flag.is {
        height:15px;
        background-position:-2501px 0px;
    }

    .iti-flag.it {
        height:14px;
        background-position:-2523px 0px;
    }

    .iti-flag.je {
        height:12px;
        background-position:-2545px 0px;
    }

    .iti-flag.jm {
        height:10px;
        background-position:-2567px 0px;
    }

    .iti-flag.jo {
        height:10px;
        background-position:-2589px 0px;
    }

    .iti-flag.jp {
        height:14px;
        background-position:-2611px 0px;
    }

    .iti-flag.ke {
        height:14px;
        background-position:-2633px 0px;
    }

    .iti-flag.kg {
        height:12px;
        background-position:-2655px 0px;
    }

    .iti-flag.kh {
        height:13px;
        background-position:-2677px 0px;
    }

    .iti-flag.ki {
        height:10px;
        background-position:-2699px 0px;
    }

    .iti-flag.km {
        height:12px;
        background-position:-2721px 0px;
    }

    .iti-flag.kn {
        height:14px;
        background-position:-2743px 0px;
    }

    .iti-flag.kp {
        height:10px;
        background-position:-2765px 0px;
    }

    .iti-flag.kr {
        height:14px;
        background-position:-2787px 0px;
    }

    .iti-flag.kw {
        height:10px;
        background-position:-2809px 0px;
    }

    .iti-flag.ky {
        height:10px;
        background-position:-2831px 0px;
    }

    .iti-flag.kz {
        height:10px;
        background-position:-2853px 0px;
    }

    .iti-flag.la {
        height:14px;
        background-position:-2875px 0px;
    }

    .iti-flag.lb {
        height:14px;
        background-position:-2897px 0px;
    }

    .iti-flag.lc {
        height:10px;
        background-position:-2919px 0px;
    }

    .iti-flag.li {
        height:12px;
        background-position:-2941px 0px;
    }

    .iti-flag.lk {
        height:10px;
        background-position:-2963px 0px;
    }

    .iti-flag.lr {
        height:11px;
        background-position:-2985px 0px;
    }

    .iti-flag.ls {
        height:14px;
        background-position:-3007px 0px;
    }

    .iti-flag.lt {
        height:12px;
        background-position:-3029px 0px;
    }

    .iti-flag.lu {
        height:12px;
        background-position:-3051px 0px;
    }

    .iti-flag.lv {
        height:10px;
        background-position:-3073px 0px;
    }

    .iti-flag.ly {
        height:10px;
        background-position:-3095px 0px;
    }

    .iti-flag.ma {
        height:14px;
        background-position:-3117px 0px;
    }

    .iti-flag.mc {
        height:15px;
        background-position:-3139px 0px;
    }

    .iti-flag.md {
        height:10px;
        background-position:-3160px 0px;
    }

    .iti-flag.me {
        height:10px;
        background-position:-3182px 0px;
    }

    .iti-flag.mf {
        height:14px;
        background-position:-3204px 0px;
    }

    .iti-flag.mg {
        height:14px;
        background-position:-3226px 0px;
    }

    .iti-flag.mh {
        height:11px;
        background-position:-3248px 0px;
    }

    .iti-flag.mk {
        height:10px;
        background-position:-3270px 0px;
    }

    .iti-flag.ml {
        height:14px;
        background-position:-3292px 0px;
    }

    .iti-flag.mm {
        height:14px;
        background-position:-3314px 0px;
    }

    .iti-flag.mn {
        height:10px;
        background-position:-3336px 0px;
    }

    .iti-flag.mo {
        height:14px;
        background-position:-3358px 0px;
    }

    .iti-flag.mp {
        height:10px;
        background-position:-3380px 0px;
    }

    .iti-flag.mq {
        height:14px;
        background-position:-3402px 0px;
    }

    .iti-flag.mr {
        height:14px;
        background-position:-3424px 0px;
    }

    .iti-flag.ms {
        height:10px;
        background-position:-3446px 0px;
    }

    .iti-flag.mt {
        height:14px;
        background-position:-3468px 0px;
    }

    .iti-flag.mu {
        height:14px;
        background-position:-3490px 0px;
    }

    .iti-flag.mv {
        height:14px;
        background-position:-3512px 0px;
    }

    .iti-flag.mw {
        height:14px;
        background-position:-3534px 0px;
    }

    .iti-flag.mx {
        height:12px;
        background-position:-3556px 0px;
    }

    .iti-flag.my {
        height:10px;
        background-position:-3578px 0px;
    }

    .iti-flag.mz {
        height:14px;
        background-position:-3600px 0px;
    }

    .iti-flag.na {
        height:14px;
        background-position:-3622px 0px;
    }

    .iti-flag.nc {
        height:10px;
        background-position:-3644px 0px;
    }

    .iti-flag.ne {
        height:15px;
        background-position:-3666px 0px;
    }

    .iti-flag.nf {
        height:10px;
        background-position:-3686px 0px;
    }

    .iti-flag.ng {
        height:10px;
        background-position:-3708px 0px;
    }

    .iti-flag.ni {
        height:12px;
        background-position:-3730px 0px;
    }

    .iti-flag.nl {
        height:14px;
        background-position:-3752px 0px;
    }

    .iti-flag.no {
        height:15px;
        background-position:-3774px 0px;
    }

    .iti-flag.np {
        height:15px;
        background-position:-3796px 0px;
    }

    .iti-flag.nr {
        height:10px;
        background-position:-3811px 0px;
    }

    .iti-flag.nu {
        height:10px;
        background-position:-3833px 0px;
    }

    .iti-flag.nz {
        height:10px;
        background-position:-3855px 0px;
    }

    .iti-flag.om {
        height:10px;
        background-position:-3877px 0px;
    }

    .iti-flag.pa {
        height:14px;
        background-position:-3899px 0px;
    }

    .iti-flag.pe {
        height:14px;
        background-position:-3921px 0px;
    }

    .iti-flag.pf {
        height:14px;
        background-position:-3943px 0px;
    }

    .iti-flag.pg {
        height:15px;
        background-position:-3965px 0px;
    }

    .iti-flag.ph {
        height:10px;
        background-position:-3987px 0px;
    }

    .iti-flag.pk {
        height:14px;
        background-position:-4009px 0px;
    }

    .iti-flag.pl {
        height:13px;
        background-position:-4031px 0px;
    }

    .iti-flag.pm {
        height:14px;
        background-position:-4053px 0px;
    }

    .iti-flag.pn {
        height:10px;
        background-position:-4075px 0px;
    }

    .iti-flag.pr {
        height:14px;
        background-position:-4097px 0px;
    }

    .iti-flag.ps {
        height:10px;
        background-position:-4119px 0px;
    }

    .iti-flag.pt {
        height:14px;
        background-position:-4141px 0px;
    }

    .iti-flag.pw {
        height:13px;
        background-position:-4163px 0px;
    }

    .iti-flag.py {
        height:11px;
        background-position:-4185px 0px;
    }

    .iti-flag.qa {
        height:8px;
        background-position:-4207px 0px;
    }

    .iti-flag.re {
        height:14px;
        background-position:-4229px 0px;
    }

    .iti-flag.ro {
        height:14px;
        background-position:-4251px 0px;
    }

    .iti-flag.rs {
        height:14px;
        background-position:-4273px 0px;
    }

    .iti-flag.ru {
        height:14px;
        background-position:-4295px 0px;
    }

    .iti-flag.rw {
        height:14px;
        background-position:-4317px 0px;
    }

    .iti-flag.sa {
        height:14px;
        background-position:-4339px 0px;
    }

    .iti-flag.sb {
        height:10px;
        background-position:-4361px 0px;
    }

    .iti-flag.sc {
        height:10px;
        background-position:-4383px 0px;
    }

    .iti-flag.sd {
        height:10px;
        background-position:-4405px 0px;
    }

    .iti-flag.se {
        height:13px;
        background-position:-4427px 0px;
    }

    .iti-flag.sg {
        height:14px;
        background-position:-4449px 0px;
    }

    .iti-flag.sh {
        height:10px;
        background-position:-4471px 0px;
    }

    .iti-flag.si {
        height:10px;
        background-position:-4493px 0px;
    }

    .iti-flag.sj {
        height:15px;
        background-position:-4515px 0px;
    }

    .iti-flag.sk {
        height:14px;
        background-position:-4537px 0px;
    }

    .iti-flag.sl {
        height:14px;
        background-position:-4559px 0px;
    }

    .iti-flag.sm {
        height:15px;
        background-position:-4581px 0px;
    }

    .iti-flag.sn {
        height:14px;
        background-position:-4603px 0px;
    }

    .iti-flag.so {
        height:14px;
        background-position:-4625px 0px;
    }

    .iti-flag.sr {
        height:14px;
        background-position:-4647px 0px;
    }

    .iti-flag.ss {
        height:10px;
        background-position:-4669px 0px;
    }

    .iti-flag.st {
        height:10px;
        background-position:-4691px 0px;
    }

    .iti-flag.sv {
        height:12px;
        background-position:-4713px 0px;
    }

    .iti-flag.sx {
        height:14px;
        background-position:-4735px 0px;
    }

    .iti-flag.sy {
        height:14px;
        background-position:-4757px 0px;
    }

    .iti-flag.sz {
        height:14px;
        background-position:-4779px 0px;
    }

    .iti-flag.ta {
        height:10px;
        background-position:-4801px 0px;
    }

    .iti-flag.tc {
        height:10px;
        background-position:-4823px 0px;
    }

    .iti-flag.td {
        height:14px;
        background-position:-4845px 0px;
    }

    .iti-flag.tf {
        height:14px;
        background-position:-4867px 0px;
    }

    .iti-flag.tg {
        height:13px;
        background-position:-4889px 0px;
    }

    .iti-flag.th {
        height:14px;
        background-position:-4911px 0px;
    }

    .iti-flag.tj {
        height:10px;
        background-position:-4933px 0px;
    }

    .iti-flag.tk {
        height:10px;
        background-position:-4955px 0px;
    }

    .iti-flag.tl {
        height:10px;
        background-position:-4977px 0px;
    }

    .iti-flag.tm {
        height:14px;
        background-position:-4999px 0px;
    }

    .iti-flag.tn {
        height:14px;
        background-position:-5021px 0px;
    }

    .iti-flag.to {
        height:10px;
        background-position:-5043px 0px;
    }

    .iti-flag.tr {
        height:14px;
        background-position:-5065px 0px;
    }

    .iti-flag.tt {
        height:12px;
        background-position:-5087px 0px;
    }

    .iti-flag.tv {
        height:10px;
        background-position:-5109px 0px;
    }

    .iti-flag.tw {
        height:14px;
        background-position:-5131px 0px;
    }

    .iti-flag.tz {
        height:14px;
        background-position:-5153px 0px;
    }

    .iti-flag.ua {
        height:14px;
        background-position:-5175px 0px;
    }

    .iti-flag.ug {
        height:14px;
        background-position:-5197px 0px;
    }

    .iti-flag.um {
        height:11px;
        background-position:-5219px 0px;
    }

    .iti-flag.us {
        height:11px;
        background-position:-5241px 0px;
    }

    .iti-flag.uy {
        height:14px;
        background-position:-5263px 0px;
    }

    .iti-flag.uz {
        height:10px;
        background-position:-5285px 0px;
    }

    .iti-flag.va {
        height:15px;
        background-position:-5307px 0px;
    }

    .iti-flag.vc {
        height:14px;
        background-position:-5324px 0px;
    }

    .iti-flag.ve {
        height:14px;
        background-position:-5346px 0px;
    }

    .iti-flag.vg {
        height:10px;
        background-position:-5368px 0px;
    }

    .iti-flag.vi {
        height:14px;
        background-position:-5390px 0px;
    }

    .iti-flag.vn {
        height:14px;
        background-position:-5412px 0px;
    }

    .iti-flag.vu {
        height:12px;
        background-position:-5434px 0px;
    }

    .iti-flag.wf {
        height:14px;
        background-position:-5456px 0px;
    }

    .iti-flag.ws {
        height:10px;
        background-position:-5478px 0px;
    }

    .iti-flag.xk {
        height:15px;
        background-position:-5500px 0px;
    }

    .iti-flag.ye {
        height:14px;
        background-position:-5522px 0px;
    }

    .iti-flag.yt {
        height:14px;
        background-position:-5544px 0px;
    }

    .iti-flag.za {
        height:14px;
        background-position:-5566px 0px;
    }

    .iti-flag.zm {
        height:14px;
        background-position:-5588px 0px;
    }

    .iti-flag.zw {
        height:10px;
        background-position:-5610px 0px;
    }

    .iti-flag {
        width:20px;
        height:15px;
        box-shadow:0px 0px 1px 0px #888;
        background-image:url("./img/flags.png");
        background-repeat:no-repeat;
        background-color:#DBDBDB;
        background-position:20px 0;
    }


    @media only screen and (-webkit-min-device-pixel-ratio: 2),
    only screen and (min--moz-device-pixel-ratio: 2),
    only screen and (-o-min-device-pixel-ratio: 2 / 1),
    only screen and (min-device-pixel-ratio: 2),
    only screen and (min-resolution: 192dpi),
    only screen and (min-resolution: 2dppx) {
        .iti-flag {
            background-image:url("./img/flags@2x.png")
        }

    }

    .iti-flag.np {
        background-color:transparent;
    }
</style>
