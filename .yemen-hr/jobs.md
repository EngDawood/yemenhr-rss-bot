https://yemenhr.com/jobs 

for example this is a tr from table 
the table selector :
body > div.bg-gray-100 > main > div > div > div.mt-0.hidden.sm\:flow-root.px-0 > div > div > div.relative > table > tbody
xpath
/html/body/div[1]/main/div/div/div[3]/div/div/div[1]/table/tbody


the tr:
selector :
body > div.bg-gray-100 > main > div > div > div.mt-0.hidden.sm\:flow-root.px-0 > div > div > div.relative > table > tbody > tr:nth-child(1)
xpath 
/html/body/div[1]/main/div/div/div[3]/div/div/div[1]/table/tbody/tr[1]
full xpath
/html/body/div[1]/main/div/div/div[3]/div/div/div[1]/table/tbody/tr[1]

or elements:
<tr wire:key="13797" class="hover:bg-blue-50/50">

                                        <td class="whitespace-nowrap px-3 py-5 text-sm text-gray-500">
                                            <span class="inline-flex items-center rounded-md  px-2 py-1 text-sm font-medium text-gray-700 "> 06 Sep, 25</span>
                                        </td>

                                        <td class="whitespace-nowrap py-5 pl-4 pr-3 text-sm sm:pl-0">
                                            <div class="flex items-center">
                                                <img class="h-14 w-14 rounded-full border-2 border-gray-200" src="/storage/logos/SI Yemen.png" alt="Solidarites Int'l">
                                                <div class="ml-4">
                                                    <div class="font-medium text-sm text-gray-400">
                                                        <a href="#" wire:click.prevent="filterByCompany(9)" class="hover:text-yemenhr-yellow hover:underline">
                                                            Solidarites Int'l
                                                        </a>
                                                    </div>
                                                </div>
                                            </div>
                                        </td>


                                        <td class="whitespace-normal px-3 py-5  text-gray-500">
                                            <div class="text-gray-900 break-words">
                                                <a href="https://yemenhr.com/jobs/mtdrbyn-solidarites-intl-multiple-cities-0ab2a058 " target="_blank" class="text-gray-700 text-lg font-bold leading-6 hover:text-yemenhr-yellow" lang="ar">
                                                    متدربين
                                                </a>
                                                <!--[if BLOCK]><![endif]-->                                                    <span class="text-xs font-normal text-yemenhr-yellow ml-2 border-b border-yemenhr-yellow">new</span>
                                                <!--[if ENDBLOCK]><![endif]-->


                                            </div>


                                        </td>




                                        <td class="whitespace-nowrap py-5 pl-4 pr-3 text-sm sm:pl-0">
                                            <div class="flex items-center">
                                                <div class="ml-4">
                                                    <div class="font-medium text-base text-gray-400 group">
                                                        <span class="group-hover:text-yemenhr-yellow">
                                                            <!--[if BLOCK]><![endif]-->                                                                <a href="#" wire:click.prevent="filterByLocation(18)" class="hover:text-yemenhr-yellow hover:underline">
                                                                    Multiple Cities
                                                                </a><!--[if BLOCK]><![endif]--><!--[if ENDBLOCK]><![endif]-->
                                                            <!--[if ENDBLOCK]><![endif]-->
                                                        </span>
                                                    </div>
                                                </div>
                                            </div>
                                        </td>





                                        <td class=" whitespace-nowrap px-3 py-5 text-sm text-gray-500"><span class="inline-flex items-center rounded-md  px-2 py-1 text-sm font-medium text-rose-300 ring-1 ring-inset ring-rose-600/10"> 30 Sep, 25</span>
                                        </td>
                                        <!-- First Tooltip -->
                                        <td class="relative whitespace-nowrap py-5 pl-0 pr-4 text-left text-sm font-medium sm:pr-0">
                                            <!-- Read/Unread Icon -->
                                            <div x-data="{ hover: false }" class="relative inline-block">
                                                <div @mouseover="hover = true" @mouseout="hover = false" class="p-1 rounded-full" onclick="window.location.href='https://yemenhr.com/login '">
                                                    <!--[if BLOCK]><![endif]-->                                                        <svg xmlns="http://www.w3.org/2000/svg " fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-gray-400" data-darkreader-inline-stroke="" style="--darkreader-inline-stroke: currentColor;">
                                                            <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z"></path>
                                                            <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z"></path>
                                                        </svg>
                                                    <!--[if ENDBLOCK]><![endif]-->
                                                </div>
                                                <div x-show="hover" x-transition="" class="absolute right-5 bottom-full mb-2 w-24 bg-gray-600 text-white text-xs rounded py-1 px-2 z-10 text-center" style="display: none;">
                                                    <!--[if BLOCK]><![endif]-->                                                        Login to mark
                                                    <!--[if ENDBLOCK]><![endif]-->
                                                </div>
                                            </div>

                                            <!-- Bookmark Icon -->
                                            <div x-data="{ hover: false }" class="relative inline-block ml-2">
                                                <div @mouseover="hover = true" @mouseout="hover = false" class="p-1 rounded-full" onclick="window.location.href='https://yemenhr.com/login '">
                                                    <!--[if BLOCK]><![endif]-->                                                        <svg xmlns="http://www.w3.org/2000/svg " fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="h-6 w-6 text-gray-400" data-darkreader-inline-stroke="" style="--darkreader-inline-stroke: currentColor;">
                                                            <path stroke-linecap="round" stroke-linejoin="round" d="M16.5 3.75V16.5L12 14.25 7.5 16.5V3.75m9 0H18A2.25 2.25 0 0 1 20.25 6v12A2.25 2.25 0 0 1 18 20.25H6A2.25 2.25 0 0 1 3.75 18V6A2.25 2.25 0 0 1 6 3.75h1.5m9 0h-9"></path>
                                                        </svg>
                                                    <!--[if ENDBLOCK]><![endif]-->
                                                </div>
                                                <div x-show="hover" x-transition="" class="absolute right-5 bottom-full mb-2 bg-yemenhr-yellow text-white text-xs rounded py-1 px-2 z-10" style="display: none;">
                                                    <!--[if BLOCK]><![endif]-->                                                        Login to save
                                                    <!--[if ENDBLOCK]><![endif]-->
                                                </div>
                                            </div>
                                        </td>

                                    </tr>



