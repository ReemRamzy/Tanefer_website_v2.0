<template>
  <v-container fluid>
    <v-row>
      <v-col cols="12" md="3" class="d-none d-md-block">
        <SideBar />
      </v-col>

      <v-col cols="12" md="9" class="main-content">
        <v-card class="booking-history-card">
          <v-card-title>
            <span class="headline">Booking History</span>
          </v-card-title>
          <!-- <v-card-text>
            <div class="table-container">
              <table class="booking-history-table">
                <thead>
                  <tr class="text-center">
                    <th>Type</th>
                    <th>Title</th>
                    <th>Order Date</th>
                    <th>Duration</th>
                    <th>Total Fees</th>
                    <th>Status</th>
                    <th>Details</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="booking in bookings" :key="booking.id">
                    <td>{{ booking.type }}</td>
                    <td>{{ booking.title }}</td>
                    <td>{{ booking.date }}</td>
                    <td>{{ booking.duration || 'N/A' }}</td>
                    <td>{{ booking.total | currency }}</td>
                    <td>{{ booking.status }}</td>
                    <td>
                      <v-btn
                        v-if="!isMobile"
                        class="details-btn"
                        @click="fetchBookingDetails(booking.id)"
                      >
                        Get Details
                      </v-btn>

                      <a
                        v-if="isMobile"
                        href="#"
                        class="details-link"
                        @click.prevent="fetchBookingDetails(booking.id)"
                      >
                        Get Details
                      </a>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>

            <div v-if="selectedBooking" class="popup-overlay" @click="closeDetails">
              <div class="popup-content" @click.stop>
                <h3>Booking Details</h3>
                <p><strong>Type:</strong> {{ selectedBooking.type }}</p>
                <p><strong>Title:</strong> {{ selectedBooking.title }}</p>
                <p><strong>Order Date:</strong> {{ selectedBooking.date }}</p>
                <p><strong>Total Price:</strong> {{ selectedBooking.total | currency }}</p>
                <p><strong>Duration:</strong> {{ selectedBooking.duration || 'N/A' }}</p>
                <p><strong>Status:</strong> {{ selectedBooking.status }}</p>
                <p><strong>Accommodation Price:</strong> {{ selectedBooking.details.accommodation_price || 'N/A' }}</p>
                <p><strong>Adults:</strong> {{ selectedBooking.details.adults }}</p>
                <p><strong>Children:</strong> {{ selectedBooking.details.children }}</p>
                <button class="details-btn" @click="closeDetails">
                  Close
                </button>
              </div>
            </div>
          </v-card-text> -->
          <v-card-text>
            <div class="table-container">
              <table class="booking-history-table">
                <thead>
                  <tr class="text-center">
                    <th>Type</th>
                    <th>Title</th>
                    <th>Order Date</th>
                    <th>Duration</th>
                    <th>Total Fees</th>
                    <th>Status</th>
                    <th>Details</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="booking in bookings" :key="booking.id">
                    <td>{{ booking.type }}</td>
                    <td>{{ booking.title }}</td>
                    <td>{{ booking.date }}</td>
                    <td>{{ booking.duration || 'N/A' }}</td>
                    <td>{{ booking.total | currency }}</td>
                    <td>{{ booking.status }}</td>
                    <td>
                      <v-btn
                        v-if="!isMobile"
                        class="details-btn"
                        @click="generatePdf(booking)"
                      >
                        Get Details
                      </v-btn>
                      <a
                        v-if="isMobile"
                        href="#"
                        class="details-link"
                        @click="generatePdf(booking)"
                      >
                        Get Details
                      </a>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div v-if="selectedBooking" class="popup-overlay" @click="closeDetails">
              <div class="popup-content" @click.stop>
                <h3>Booking Details</h3>
                <p><strong>Type:</strong> {{ selectedBooking.type }}</p>
                <p><strong>Title:</strong> {{ selectedBooking.title }}</p>
                <p><strong>Order Date:</strong> {{ selectedBooking.date }}</p>
                <p><strong>Total Price:</strong> {{ selectedBooking.total | currency }}</p>
                <p><strong>Duration:</strong> {{ selectedBooking.duration || 'N/A' }}</p>
                <p><strong>Status:</strong> {{ selectedBooking.status }}</p>
                <p><strong>Accommodation Price:</strong> {{ selectedBooking.details.accommodation_price || 'N/A' }}</p>
                <p><strong>Adults:</strong> {{ selectedBooking.details.adults }}</p>
                <p><strong>Children:</strong> {{ selectedBooking.details.children }}</p>
                <button class="details-btn text--white" @click="closeDetails">
                  Close
                </button>
              </div>
            </div>
            <!-- Pagination Controls -->
            <v-pagination
              v-model="currentPage"
              :length="totalPages"
              :total-visible="7"
              @input="fetchBookings"
            />
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { mapGetters } from 'vuex'
import jsPDF from 'jspdf'
// eslint-disable-next-line no-unused-vars
import autoTable from 'jspdf-autotable'
import clientAPI from '../services/axiosConfig'

export default {
  filters: {
    currency (value) {
      if (!value) { return '' }
      return `$${value.toFixed(2)}`
    }
  },
  data () {
    return {
      selectedBooking: null,
      bookings: [],
      userInfo: null,
      currentPage: 1,
      totalPages: 1,
      rowPerPage: 10
    }
  },
  computed: {
    ...mapGetters({
      user: 'auth/user'
    }),
    isMobile () {
      return window.innerWidth <= 768
    }
  },
  async mounted () {
    await this.fetchBookings()
  },
  methods: {
    async fetchBookings () {
      try {
        const token = localStorage.getItem('authToken')
        const response = await clientAPI('https://api.tanefer.com/api/v2/booking').get('/histories', {
          headers: {
            Authorization: `Bearer ${token}`
          },
          params: {
            page: this.currentPage, // Pass the current page to the backend
            row_per_page: this.rowPerPage // Pass rows per page to the backend
          }
        })

        if (response.data.status === 200) {
          const responseData = response.data.data
          this.bookings = responseData.historyList
          this.totalPages = Math.ceil(responseData.historyTotal / this.rowPerPage) // Calculate total pages
          this.userInfo = responseData.user
        } else {
          console.error('Unexpected response:', response)
        }
      } catch (error) {
        console.error('Error fetching booking histories:', error)
        alert('Error fetching booking histories.')
      }
    },
    async fetchBookingDetails (id) {
      try {
        const token = localStorage.getItem('authToken')
        const response = await clientAPI('https://api.tanefer.com/api/v2/booking').get(`/history-details/${id}`, {
          headers: {
            Authorization: `Bearer ${token}`
          }
        })

        if (response.status === 200) {
          this.selectedBooking = response.data.data
        } else {
          console.error('Unexpected response status:', response.status)
          alert('Unexpected error occurred while fetching booking details.')
        }
      } catch (error) {
        console.error('Error fetching booking details:', error)
        alert('Error fetching booking details.')
      }
    },
    closeDetails () {
      this.selectedBooking = null
    },
    // this.decodeHtmlEntities (text) {
    //   const textArea = document.createElement('textarea')
    //   textArea.innerHTML = text
    //   return textArea.value
    // },
    // Function to decode HTML entities

    decodeHtmlEntities (html) {
      const txt = document.createElement('textarea')
      txt.innerHTML = html
      return txt.value
    },

    generatePdf (booking) {
      // eslint-disable-next-line new-cap
      const doc = new jsPDF()
      const marginLeft = 10
      const pageHeight = doc.internal.pageSize.height // Page height for handling overflow
      let currentY = 20

      const addNewPageIfNeeded = (currentY, doc) => {
        if (currentY + 10 > pageHeight) {
          doc.addPage()
          return 20 // Reset Y position for the new page
        }
        return currentY
      }

      // Title
      doc.setFontSize(20)
      doc.setFont('helvetica', 'bold')
      doc.setTextColor(160, 132, 88) // Gold color
      doc.text('Booking Details', marginLeft, currentY)
      currentY += 15

      // General Details
      const generalDetails = [
        ['Field', 'Details'],
        ['Type', booking.type || 'N/A'],
        ['Title', this.decodeHtmlEntities(booking.title) || 'N/A'],
        ['Order Date', booking.date || 'N/A'],
        ['Duration', booking.duration || 'N/A'],
        ['Total Fees', `$${booking.total?.toFixed(2) || 'N/A'}`],
        ['Status', booking.status || 'N/A']
      ]
      doc.autoTable({
        startY: currentY,
        head: [generalDetails[0]],
        body: generalDetails.slice(1),
        styles: { fontSize: 10, cellPadding: 3, textColor: ['#966B47'] }, // Darker Brown
        headStyles: { fillColor: [160, 132, 88], textColor: [255, 255, 255] }, // Gold Header
        alternateRowStyles: { fillColor: [245, 235, 215] } // Light Beige Rows
      })
      currentY = doc.lastAutoTable.finalY + 10

      // Hotel Details
      if (booking.type === 'hotel' && booking.hotel) {
        const hotel = booking.hotel
        currentY = addNewPageIfNeeded(currentY, doc)

        doc.setFontSize(16)
        doc.setTextColor(160, 132, 88) // Gold color
        doc.text('Hotel Details', marginLeft, currentY)
        currentY += 10

        const hotelDetails = [
          ['Field', 'Details'],
          ['Hotel Name', this.decodeHtmlEntities(hotel.hotel_name || 'N/A')],
          ['Address', this.decodeHtmlEntities(hotel.address || 'N/A')],
          ['Locator', hotel.locator || 'N/A'],
          ['Room Type', this.decodeHtmlEntities(hotel.room_type || 'N/A')],
          ['Check-In', hotel.check_in || 'N/A'],
          ['Check-Out', hotel.check_out || 'N/A'],
          ['Total Price', hotel.total_price || 'N/A'],
          ['Board Type', this.decodeHtmlEntities(hotel.board_type || 'N/A')]
        ]
        doc.autoTable({
          startY: currentY,
          head: [hotelDetails[0]],
          body: hotelDetails.slice(1),
          styles: { fontSize: 10, cellPadding: 3, textColor: ['#966B47'] },
          headStyles: { fillColor: [160, 132, 88], textColor: [255, 255, 255] },
          alternateRowStyles: { fillColor: [245, 235, 215] }
        })
        currentY = doc.lastAutoTable.finalY + 10

        // Add Cancellation Policy
        if (hotel.cancellation_policy) {
          currentY = addNewPageIfNeeded(currentY, doc)
          doc.setFontSize(10)
          doc.setTextColor(160, 132, 88) // Gold color
          doc.text('Cancellation Policy', marginLeft, currentY)
          currentY += 10

          const policyText = this.decodeHtmlEntities(
            hotel.cancellation_policy.replace(/<[^>]*>/g, '') || 'N/A'
          )
          const lines = doc.splitTextToSize(policyText, 190)
          lines.forEach((line) => {
            currentY = addNewPageIfNeeded(currentY, doc)
            doc.text(line, marginLeft, currentY)
            currentY += 8
          })
        }

        // Add Comments
        if (hotel.comments) {
          currentY = addNewPageIfNeeded(currentY, doc)
          doc.setFontSize(10)
          doc.setTextColor(160, 132, 88) // Gold color
          doc.text('Other', marginLeft, currentY)
          currentY += 10

          const commentsText = this.decodeHtmlEntities(
            hotel.comments.replace(/<[^>]*>/g, '') || 'N/A'
          )
          const lines = doc.splitTextToSize(commentsText, 190)
          lines.forEach((line) => {
            currentY = addNewPageIfNeeded(currentY, doc)
            doc.text(line, marginLeft, currentY)
            currentY += 8
          })
        }
      }

      // Adventures Section
      if (booking.type === 'adventure' && booking.adventures) {
        const adventure = booking.adventures
        doc.setFontSize(16)
        doc.setTextColor(160, 132, 88) // Gold color (A08458)
        doc.text('Adventure Details', marginLeft, currentY)
        currentY += 10

        const adventureDetails = [
          ['Field', 'Details'],
          ['Overview', this.decodeHtmlEntities(adventure.overview?.replace(/<[^>]*>/g, '') || 'N/A')],
          ['Intro', this.decodeHtmlEntities(adventure.intro?.replace(/<[^>]*>/g, '') || 'N/A')],
          ['Itinerary', this.decodeHtmlEntities(adventure.itinerary?.replace(/<[^>]*>/g, '') || 'N/A')],
          ['Includes', JSON.parse(adventure.includes || '[]').join('\n- ') || 'N/A'],
          ['Excludes', JSON.parse(adventure.excludes || '[]').join('\n- ') || 'N/A'],
          ['Activity Type', adventure.activity_type || 'N/A']
        ]
        doc.autoTable({
          startY: currentY,
          head: [adventureDetails[0]],
          body: adventureDetails.slice(1),
          styles: { fontSize: 10, cellPadding: 3, textColor: ['#966B47'] },
          headStyles: { fillColor: [160, 132, 88], textColor: [255, 255, 255] },
          alternateRowStyles: { fillColor: [245, 235, 215] }
        })
        currentY = doc.lastAutoTable.finalY + 10
      }

      // Cruise Section
      if (booking.type === 'cruise' && booking.cruise) {
        const cruise = booking.cruise
        doc.setFontSize(16)
        doc.setTextColor(160, 132, 88) // Gold color (A08458)
        doc.text('Cruise Details', marginLeft, currentY)
        currentY += 10

        const cruiseDetails = [
          ['Field', 'Details'],
          ['Name', this.decodeHtmlEntities(cruise.name || 'N/A')],
          ['Cruise Line', this.decodeHtmlEntities(cruise.cruise_line || 'N/A')],
          ['Ship Name', this.decodeHtmlEntities(cruise.ship_name || 'N/A')],
          ['Stars', cruise.stars?.toString() || 'N/A'],
          ['Number of Nights', cruise.number_of_nights?.toString() || 'N/A'],
          ['Facilities', cruise.facilities?.join('\n- ') || 'N/A'],
          ['Policies', cruise.policies?.join('\n- ') || 'N/A'],
          ['Includes', cruise.includes?.join('\n- ') || 'N/A'],
          ['Excludes', cruise.excludes?.join('\n- ') || 'N/A']
        ]
        doc.autoTable({
          startY: currentY,
          head: [cruiseDetails[0]],
          body: cruiseDetails.slice(1),
          styles: { fontSize: 10, cellPadding: 3, textColor: ['#966B47'] },
          headStyles: { fillColor: [160, 132, 88], textColor: [255, 255, 255] },
          alternateRowStyles: { fillColor: [245, 235, 215] }
        })
        currentY = doc.lastAutoTable.finalY + 10

        // Add Description
        currentY = addNewPageIfNeeded(currentY, doc)
        doc.setFontSize(10)
        doc.setTextColor(160, 132, 88) // Gold color
        doc.text('Cruise Description', marginLeft, currentY)
        currentY += 10

        currentY = addNewPageIfNeeded(currentY, doc)
        doc.setFontSize(10)
        doc.setTextColor('#966B47') // Darker shade (966B47)
        const description = this.decodeHtmlEntities(cruise.description?.replace(/<[^>]*>/g, '') || 'N/A')
        const lines = doc.splitTextToSize(description, 190)
        lines.forEach((line) => {
          currentY = addNewPageIfNeeded(currentY, doc)
          doc.text(line, marginLeft, currentY)
          currentY += 8
        })
      }

      // Save PDF
      // doc.save(`${this.decodeHtmlEntities(booking.title) || 'Booking'}.pdf`)
      const pdfBlob = doc.output('blob')
      const pdfUrl = URL.createObjectURL(pdfBlob)
      window.open(pdfUrl, '_blank')
    }

  }
}
</script>

<style scoped>
.main-content {
  margin-left: 240px;
  padding: 20px;
}

.booking-history-table {
  width: 100%;
  border-collapse: collapse;
}

.booking-history-table th, .booking-history-table td {
  border: 1px solid #ddd;
  padding: 8px;
}

.booking-history-table th {
  background-color: #f2f2f2;
}

.table-container {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

.booking-history-card {
  height: 100%;
  min-height: 400px;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2000;
}

.popup-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  width: 400px;
  max-width: 90%;
  z-index: 2001;
}

.details-btn {
  background-color: #4f3316;
  color: #fff;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  font-weight: 300;
  border-radius: 3px;
}

.theme--light.v-btn.v-btn--has-bg {
  background-color: #4f3316 !important;
}

.details-btn:hover {
  background-color: #3c2d1c;
}

.text-center {
  text-align: center;
}

@media (max-width: 768px) {
  .main-content {
    margin-left: 0;
    padding: 10px;
  }

  .popup-content {
    width: 100%;
    max-width: 100%;
  }
  .v-card__text {
    margin: 0px;
    padding: 0px;
    font-size: 8px;
  }
}
tr {
  text-align: center;
}
.details-link {
  color: brown;
  text-decoration: underline;
  cursor: pointer;
}

.details-link:hover {
  text-decoration: none;
}
</style>
